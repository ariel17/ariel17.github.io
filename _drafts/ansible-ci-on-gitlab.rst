.. title: Ansible CI on GitLab
.. slug: ansible-ci-on-gitlab
.. date: 2017-02-05 22:32:17 UTC-03:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Using a Docker container running a SSH daemon as idle server, Ansible can run
playbooks safely in an ephemeral environment. GitLab CI provides a highly
flexible interface as recipe with |gitlab-ci|_ file:

.. code:: yaml

   image: ansible/ubuntu14.04-ansible

   variables:
     GIT_STRATEGY: clone
   
   before_script:
     - eval $(ssh-agent -s)
     - mkdir -p ~/.ssh
     - echo "$SSH_PRIVATE_KEY" > ~/.ssh/id_rsa
     - chmod 400 ~/.ssh/id_rsa
     - echo 'Host github.com' > ~/.ssh/config
     - echo '    User git' >> ~/.ssh/config
     - echo '    IdentityFile ~/.ssh/id_rsa' >> ~/.ssh/config
     - echo '    IdentitiesOnly yes' >> ~/.ssh/config
     - echo '    StrictHostKeyChecking no' >> ~/.ssh/config
     - git submodule init
     - git submodule update
     - echo "$ANSIBLE_VAULT_PASSWORD" > "$ANSIBLE_VAULT_PASSWORD_FILE"
     - apt-get install sshpass -y

   after_script:
     - rm -rf .ssh "$ANSIBLE_VAULT_PASSWORD_FILE"
   
   stages:
     - build
     - test
     - production
   
   syntax_check:
     stage: build
     script:
       - ansible-playbook --syntax-check playbook.yml
   
   server01:
     stage: test
     services:
       - rastasheep/ubuntu-sshd:14.04
     environment: testing
     script:
       - echo '[server01]' >> inventory
       - echo 'rastasheep__ubuntu-sshd ansible_connection=ssh ansible_user=root ansible_ssh_pass=root' >> inventory
       - ansible server01 -m setup
       - ansible-playbook playbook.yml --limit server01

The cheapest test that can be done is syntax checking. Once it is verified,
test jobs will be fired in parallel (there can be any number of jobs in each
stage), each one with an idle server asociated to it. `$SSH_PRIVATE_KEY`,
`$ANSIBLE_VAULT_PASSWORD` and `$ANSIBLE_VAULT_PASSWORD_FILE` are variables
provided by GitLab itself, configured previously by the user. For the idle
server I used the ubuntu-sshd_ Docker image as service only for test jobs. The
inventory is overrided to match configuration for every node.

.. |gitlab-ci| replace:: `.gitlab-ci.yml`
.. _gitlab-ci: https://docs.gitlab.com/ce/ci/quick_start/#quick-start
.. _ubuntu-sshd: https://github.com/rastasheep/ubuntu-sshd
