.. title: Nginx como balanceador de carga
.. slug: nginx-como-balanceador-de-carga
.. date: 2017-05-16 22:44:51 UTC-03:00
.. tags: nginx, web, docker
.. category: 
.. link: http://nginx.org/en/docs/http/load_balancing.html
.. description: 
.. type: text

Suponiendo una aplicación donde se quiera distribuir el tráfico sobre distintas
instancias para soportar la carga, es posible usar Nginx_ como servidor web y
al mismo tiempo balanceador de carga.

Arquitectura
------------

* 1 Nginx
* 3 simple aplicaciones web como backends 

.. code-block:: yaml

   # docker-compose.yml
   ---
   version: '3'
   services:
     nginx:
       build:
         context: .
       ports:
         - 8080:80
       links:
         - backend1
         - backend2
         - backend3
       depends_on:
         - backend1
         - backend2
         - backend3
   
     backend1:
       image: python
       command: python -m http.server 80
   
     backend2:
       image: python
       command: python -m http.server 80
   
     backend3:
       image: python
       command: python -m http.server 80

.. code-block:: Dockerfile
 
   # Dockerfile
   FROM nginx
   LABEL maintainer "ariel.gerardo.rios@gmail.com"
   
   COPY ["nginx.conf", "/etc/nginx/nginx.conf"]

.. code-block::

   # nginx.conf
   events{}

   http {
       upstream backend {
           server backend1;
           server backend2;
           server backend3;
       }
   
       log_format upstreamlog '[$time_local] upstream $upstream_addr: $request upstream_response_time $upstream_response_time msec $msec request_time $request_time';
   
       server {
           listen 80;
           access_log /dev/stdout upstreamlog;
   
           location / {
               proxy_pass http://backend;
           }
       }
   }

Simulación
----------

El método de selección aplicado por defecto es Round Robin.

.. code-block:: bash

   ~$ docker-compose build && docker-compose up  # localhost:8080
   ...
   nginx_1     | [17/May/2017:01:56:50 +0000] upstream 172.30.0.2:80: GET / HTTP/1.1 upstream_response_time 0.003 msec 1494986210.108 request_time 0.003
   nginx_1     | [17/May/2017:01:56:50 +0000] upstream 172.30.0.3:80: GET /favicon.ico HTTP/1.1 upstream_response_time 0.001 msec 1494986210.130 request_time 0.001
   nginx_1     | [17/May/2017:01:56:51 +0000] upstream 172.30.0.4:80: GET / HTTP/1.1 upstream_response_time 0.002 msec 1494986211.914 request_time 0.002
   nginx_1     | [17/May/2017:01:56:52 +0000] upstream 172.30.0.2:80: GET / HTTP/1.1 upstream_response_time 0.004 msec 1494986212.142 request_time 0.004
   nginx_1     | [17/May/2017:01:56:52 +0000] upstream 172.30.0.3:80: GET / HTTP/1.1 upstream_response_time 0.009 msec 1494986212.396 request_time 0.009
   ...

Se puede alterar el método de selección modificando el peso de los nodos:

.. code-block::

     upstream backend {
         server backend1 weight=5;
         server backend2;
         server backend3;
     }

Otros métodos
-------------

* Persistencia de sesiones:

  .. code-block::

     upstream backend {
         ip_hash;
         server backend1;
         server backend2;
         server backend3;
     }

* Menos conectado:

  .. code-block::

     upstream backend {
         least_conn;
         server backend1;
         server backend2;
         server backend3;
     }


Más información
---------------

|docs|_

.. _Nginx: https://www.nginx.com/
.. |docs| replace:: Documentación de Nginx.
.. _docs: http://nginx.org/en/docs/http/load_balancing.html
