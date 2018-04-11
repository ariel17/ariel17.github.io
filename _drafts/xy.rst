.. title: (x,y) parte 1
.. slug: xy-parte-1
.. date: 2017-07-01 21:34:51 UTC-03:00
.. tags: go, android, idea
.. category: 
.. link: 
.. description: 
.. type: text

Quiero hacer un proyecto personal en Go, también quiero hacer uno en Android, y
para aprovechar ambas intenciones voy a usarlas en un mismo objetivo: **un
sistema de rastreo de personas**.

Definición del *problema*
-------------------------

Un sistema capaz de proveer a un usuario la informacion geográfica de ciertos
individuos (previamente indicados por éste) y ubicarlos en un mapa. En
condiciones ideales, el margen de error respecto a la posición real de la
persona no debe superar los 60 segundos. Se debe guardar el historial de
posiciones de los individuos registrados durante el período de 1 día, aún si el
usuario no está utilizando el sistema.

Análisis
--------

#. Se utilizará el GPS de los dispositivos Android que los individuos porten
   para obtener la información geográfica. Para obtener acceder a dicha
   información es necesario una aplicación que pueda capturarla y enviarla a un
   punto de reporte.

#. Siendo que la idea es tener información de los individuos aún cuando el
   usuario no esté usando el sistema, será necesario tener un punto de
   comunicación en común para las partes que idealmente esté siempre
   disponible. Se usará un VPS para proveer dicha funcionalidad.

#. Siguiendo la línea del punto anterior, se implementará una API REST para la
   comunicación entre el server y la aplicación Andriod.

#. El usuario deberá tener una interfaz en la cual pueda cargar datos de
   identificación de los individuos a monitorear. La misma será una aplicación
   web que almacene los datos y los disponibilice en el sistema.

#. La interfaz web también será el medio por el cual se visualizará la posición
   de los individuos, comunicándose con la API para obtener las
   actualizaciones.

.. image:: https://raw.githubusercontent.com/ariel17/xy/master/docs/sequence.mmd.png

Observasciones técnicas
-----------------------

* No se van a usar frameworks.
* El componente API y Web del sistema serán implementados en Go.

Gestión del proyecto
--------------------

* Repositorio: |repo|_
* `Milestones <https://github.com/ariel17/xy/milestones>`_.

.. |repo| replace:: ariel17/xy
.. _repo: https://github.com/ariel17/xy

Otras entradas
--------------

* |2|_

.. |2| replace:: (x,y) parte 2
.. _2: /blog/xy-parte-2/
