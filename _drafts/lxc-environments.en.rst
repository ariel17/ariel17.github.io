.. title: Building environments with Linux Containers
.. slug: lxc-environments
.. date: 2014/08/02 10:07:23
.. tags: linux, development, virtualization
.. link: 
.. description: Build development environments with LXC
.. type: text

Thanks to virtualization tools is too easy today to quickly build multiple
instance environments. VMWare_ or VirtualBox_ are good options when the
operative system to execute on the virtual machine is distinct from the host
system, but when the objective is to virtualize Linux over Linux the option
spectrum opens to other *cheaper* options in the resource consumption.

Linux Containers
================

If we want to build (or replicate) an ambient consisting only of Linux
instances there is a tool named LXC_: Linux Containers. It is not a virtual
machine but it provides a virtual *environment*, separating process and net
spaces, functionallity provided by the Linux kernel itself since versio
2.6.29 with cgroups_ integration. Without the full virtual machine simulation
overhead there are more available resources to be used.

You can get more information about it in the `official LXC page`_.

Installation
------------

We can install LXC directly from the software repository of our elected
distribution:

.. code-block:: bash

   ~$ sudo apt-get install lxc  # for Debian-based distros

Also we can install the *bleeding edge* version directly from the `GitHub
repository`_:

.. code-block:: bash

   ~$ sudo apt-get install git  # if you don't have installed git yet
   ~$ git clone git@github.com:lxc/lxc.git
   ~$ sudo su
   #$ cd lxc && ./autogen.sh && ./configure && make && sudo make install

Creating instances
------------------

The container creation can be done through templates or manually indicating
which image to use.

.. code-block:: bash

   ~$ lxc-create -n ubuntu1 -t ubuntu  # creates a container named ubuntu1 using the ubuntu template
   ~$ lxc-create -n ubuntu2 -t ubuntu-cloud -- -r trusty -T http://cloud-images.ubuntu.com/trusty/current/trusty-server-cloudimg-arm64-root.tar.gz

This way, the creation is done without any other intervention from user. There
is also a way for interactive creation, visualizing distributions and
available architectures:

.. code-block:: bash

   ~$ lxc-create -t download -n ubuntu3

Displaying created instances
----------------------------

To show available instances you can make use of :code:`lxc-ls` command. It will
only display only the names but using the :code:`--fancy` flag it will append
more useful information about them:

.. code-block:: bash

   ~$ sudo lxc-ls --fancy

   NAME     STATE    IPV4        IPV6  AUTOSTART
   ---------------------------------------------
   ubuntu1  RUNNING  10.0.3.228  -     NO
   ubuntu2  RUNNING  10.0.3.147  -     NO
   ubuntu3  STOPPED  -           -     NO

Accessing an instance
---------------------

Getting access to the instance terminal can be done with the command
:code:`lxc-console -n <instance-name>`. Executing this command is the same to
gain access to the host's phisical terminal; **it is not a remote connection**.
To close the terminal connection is enough with the :code:`Ctrl-a+q` input.

.. code-block:: bash

   ~$ sudo lxc-console -n db  # enter Ctrl-a+q to close the session

A web console to rule them all
==============================

There is a project called `LXC Web Panel`_ (a `fork from original project`_
for lxc>=1.0 versions) that is just a web site for Linux containers management.
It control tools but also statistic reports for resource consumption 
visualization for all; guests and host.

Installing Web Panel
--------------------

To install the web panel we must add the package to our local software
repository, install it and change the default configurations if we consider it
necesary to do:

.. code-block:: bash

   ~$ wget -O - http://claudyus.github.io/LXC-Web-Panel/claudyus.gpg.key | sudo apt-key add -
   ~$ echo "deb http://claudyus.github.io/LXC-Web-Panel/ ./" | sudo tee /etc/apt/sources.list.d/lwp.list
   ~$ sudo apt-get update
   ~$ sudo apt-get install lwp
   ~$ sudo cp /etc/lwp/lwp.example.conf /etc/lwp/lwp.conf  # using default configuration
   ~$ sudo service lwp start

I will assume that default settings were used; if change were made do not
forget to adapt them to the examples exposed here to you particular cases. Once
the installation finishes, open a browser and go to the http://localhost:5000/
web address:

.. class:: thumbnail
.. figure:: /galleries/lxc-for-development/login.png
   :width: 100 %
   :scale: 80 %
   :alt: Login en LXC Web Panel.
   :align: center

Using :code:`admin` as default user and password, we can login to the main
panel and it will show us a resource consumption visualization for guests and
hosts; links to instances and networking settings:

.. class:: thumbnail
.. figure:: /galleries/lxc-for-development/dashboard.png
   :width: 100 %
   :scale: 80 %
   :alt: Dashboard on LXC Web Panel.
   :align: center

Accessing to any instance listed on the left pane we will see information
related to it, as execution staus, network properties, assigned processors,
memory limits, image location, etc.
   
.. class:: thumbnail
.. figure:: /galleries/lxc-for-development/instance.png
   :width: 100 %
   :scale: 80 %
   :alt: Instance configuration on LXC Web Panel.
   :align: center

Building a custom schema
========================

I recommend to read the `Exploring LXC Networking`_ post where it exemplifies
in a deeper way each networking setting combination according to the scenario
you want to set, as outside and/or between-node conectivity and use of existing
interfaces.

Other related technologies
==========================

* OpenVZ_
* Linux-VServer_
* Docker_ (based on LXC)

.. _VMWare: http://www.vmware.com/ 
.. _VirtualBox: https://www.virtualbox.org/ 
.. _OpenVZ: http://openvz.org/
.. _Linux-VServer: http://linux-vserver.org/ 
.. _LXC: https://linuxcontainers.org/
.. _Docker: https://www.docker.com/ 
.. _cgroups: https://www.kernel.org/doc/Documentation/cgroups/cgroups.txt
.. _`official LXC page`: LXC_
.. _`GitHub repository`: https://github.com/lxc/lxc
.. _`LXC Web Panel`: http://claudyus.github.io/LXC-Web-Panel/
.. _`fork from original project`: http://lxc-webpanel.github.io/
.. _`página oficial del panel`: `LXC Web Panel`_
.. _`Exploring LXC Networking`: http://containerops.org/2013/11/19/lxc-networking/
