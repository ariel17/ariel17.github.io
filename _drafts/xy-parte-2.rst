.. title: (x,y) parte 2
.. slug: xy-parte-2
.. date: 2017-07-09 19:20:46 UTC-03:00
.. tags: go, android, idea
.. category: 
.. link: 
.. description: 
.. type: text

El código del proyecto lo dividí en 3 directorios, conteniendo las partes más
significativas del mismo.

.. code:: text

   ./xy
     ├── android
     ├── api
     └── web

Android
-------

La aplicación es por ahora sólamente un formulario que solicita un PIN
existente y lo envía a otro Intent. Es el template para que en la siguiente
iteración agregue la comunicación con la API y reporte de errores de la misma.

.. raw:: html

   <style>
     .android {
       margin-right: 20px;
     }
   </style>

.. image:: /galleries/xy/android1.png
   :class: android

.. image:: /galleries/xy/android2.png
   :class: android

.. image:: /galleries/xy/android3.png
   :class: android

API
---

Hasta el momento, la API sólo responde de manera simulada el procesamiento de
los datos que recibe.

Subjects
~~~~~~~~

* URL: `/subjects`

Representa a las operaciones relacionadas con las personas o cosas que se
desea seguir en la superficie.

.. code:: bash

   ~/xy/api$ go run main.go 9090  # terminal 1
   ~/xy/api$ curl -i -X POST http://localhost:9090/subjects  # terminal 2
   HTTP/1.1 201 Created
   Content-Type: application/json
   Date: Sun, 09 Jul 2017 22:26:37 GMT
   Content-Length: 91

   {"Current":{"Latitude":0,"Longitude":0,"CreatedAt":"0001-01-01T00:00:00Z"},"History":null}

Registration
~~~~~~~~~~~~

* URL: `/registration`

Es el punto de registro de los dispositivos a seguir. El pin es un valor
alfanumérico que se supone ya creado por la misma plataforma y asociado a un
usuario existente que quiere agregar ese dispositivo. La operación asocia el
mismo con el usuario y provee un ID único para facilitar la entrega de los
datos de localización.

.. code:: bash

   ~/xy/api$ go run main.go 9090  # terminal 1
   ~/xy/api$ curl -i -X POST http://localhost:9090/register --data "pin=1234"  # terminal 2
   HTTP/1.1 201 Created
   Content-Type: application/json
   Date: Sun, 09 Jul 2017 22:29:38 GMT
   Content-Length: 98

   {"success":true,"message":"Successfully created ID.","id":"65336dad-9e7f-4c7a-9f5b-04eeb1a5149c"}

Se hace evidente que `/register` es un recurso que pertenece a `/subjects`; lo
moveré en la siguiente iteración.

Web
---

Se configuró el sistema de template nativo de Go (a forma de prueba de
concepto) y los paquetes NPM a usar en el futuro: Grunt_, Bower_.

Otras entradas
--------------

* |1|_

.. _Grunt: https://gruntjs.com/
.. _Bower: https://bower.io/
.. |1| replace:: (x,y) parte 1
.. _1: /blog/xy-parte-1/
