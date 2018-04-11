.. title: PHP v5.6.4 & Docker
.. slug: php-v564-docker
.. date: 2015-09-20 22:46:47 UTC-03:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Last week I was 2 days -I repeat: TWO DAYS- fighting with PHP v5.6.4 and
Docker. This PHP version is the default for Ubuntu v15.04 and despite all those
who suspire when they count |fixes|_ on that version it made me cry
inconsolably when I couldn't configure the error logging.

Using this container configuration:

.. code-block:: docker

   FROM ubuntu:15.04
   MAINTAINER Ariel Gerardo Ríos <ariel.gerardo.rios@gmail.com>
   MAINTAINER Carlos Damián Ríos <carlos.damian.rios@gmail.com>
   
   RUN ["apt-get", "update"]
   RUN ["apt-get", "install", "-y", "php5-cli", "php5-gd", "php5-fpm", "php5-mcrypt", "php5-mysql", "php5-curl", "libssh2-php"]
   RUN ["php5enmod", "mcrypt"]
   
   COPY ["www.conf", "/etc/php5/fpm/pool.d/www.conf"]
   
   RUN ["mkdir", "-p", "/4ws/pilot"]
   WORKDIR /4ws/pilot
   VOLUME ["/4ws/pilot"]
   
   EXPOSE 9000
   
   CMD ["php5-fpm", "-F", "-R"]

with this FPM pool configuration:

.. code-block:: ini

   [www]
   
   user = root
   group = root
   
   listen = 9000
   
   pm = dynamic
   pm.max_children = 5
   pm.start_servers = 2
   pm.min_spare_servers = 1
   pm.max_spare_servers = 3
   pm.status_path = /status
   
   ping.path = /ping
   
   access.log = /var/log/$pool.access.log
   access.format = "%R - %u %t \"%m %r%Q%q\" %s %f %{mili}d %{kilo}M %C%%"
    
   chdir = /
    
   catch_workers_output = yes
   
   php_flag[display_errors] = on
   php_admin_value[error_log] = /var/log/fpm-php.www.log
   php_admin_flag[log_errors] = on

there was no way to make it react to save logs; all the response for any
request was a plain HTTP 500 without details on response body or log files.

Not wanting to compile PHP from scratch, the fastest solution was to downgrade
Ubuntu to version 14.04 since this one has the PHP v5.5 as default:

.. code-block:: docker

   FROM ubuntu:14.04

And it works flawlessly.

.. |fixes| replace:: the amount of fixes
.. _fixes: http://php.net/ChangeLog-5.php
