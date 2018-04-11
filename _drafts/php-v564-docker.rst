.. title: PHP v5.6.4 & Docker
.. slug: php-v564-docker
.. date: 2015-09-20 22:46:47 UTC-03:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

La semana pasada estuve 2 días -repito: DOS DÍAS- peleando con PHP v5.6.4 y
Docker. Esa versión de PHP es la que viene por defecto en los repositorios de
Ubuntu v15.05 y a pesar de todos los que suspiran cuando cuentan |fixes|_ que
tuvo esa versión a mí me hizo llorar desconsoladamente cuando no logré
configurar el logueo de errores.

Usando esta configuración para el contenedor:

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

con esta configuración del pool de FPM:

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

no había forma de hacerlo reaccionar para que guardara los logs; cuanta
respuesta obtenía para todos las requisiciones era un HTTP 500 sin detalles en
el cuerpo de la respuesta o en los archivos de logs.

Sin querer compilar PHP desde cero, la solución más rápida fue degradar la
versión de Ubuntu a la 14.04 que tiene como versión por defecto de PHP la 5.5:

.. code-block:: docker

   FROM ubuntu:14.04

Y santo remedio.

.. |fixes| replace:: la cantidad de correcciones
.. _fixes: http://php.net/ChangeLog-5.php
