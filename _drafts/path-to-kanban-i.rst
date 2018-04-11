.. title: Camino a Kanban I
.. slug: path-to-kanban-i
.. date: 2016-02-12 09:27:08 UTC-03:00
.. tags: management
.. category:
.. link: 
.. description: 
.. type: text

`Ir al siguiente post: Camino a Kanban II <http://ariel17.com.ar/posts/path-to-kanban-ii/>`_

Hace ya varias semanas que vengo leyendo el libro |Kanban|_ del señor
|Anderson|_ y me siento muy motivado por él. Al principio lo agarré y pensé
*oooootra metodología más, otra que viene a salvar el mundo y los proyectos con
su agilidad y promesas* pero durante la lectura hubieron varios puntos
atractivos:

* *Tirar* de la lista de pendientes en vez de *empujar*.
* Reducción del multitasking.
* Previsibilidad.
* Creación de tiempos libres para estudiar y/o mejorar los procesos.

Estado actual
-------------

En Movile_ trabajo como coordinador del equipo de Desarrollo de nuestro país.
Organizacionalmente estamos asignados para resolver las tareas varios paises
del cono sur en los que tenemos injerencia. Para organizar el trabajo
utilizamos sprints semanales llamados *Releases* en los que se plantean las
tareas más importantes a atacar, que pueden estar o no en el backlog:
integraciones con plataformas de terceros, productos nuevos, mejoras, bugs,
etc.

Durante la semana se atacan esas tareas priorizadas y trata de entregar el 100%
de las tareas.

.. container::

   .. image:: /galleries/path-to-kanban-i/current-flow.jpg
      :class: img-responsive
      :alt: Flujo actual
      :align: center

Listado en pasos, sería como el siguiente:

1. Las tareas se crean y se depositan en la cola del backlog.
2. Durante la reunión de release se seleccionan y priorizan las tareas a atacar.
3. Es posible que durante la duración del sprint lleguen tareas de máxima
   prioridad (llamadas blockers) que deben ser atendidas de inmediato. La
   duración de estas tareas depende mucho de la naturaleza de la misma.
4. Durante el sprint se toman tareas asignadas a la release para análisis y
   desarrollo de las mismas.
5. Desarrollada la tarea, se realizan pruebas locales para validar su
   funcionamiento. En caso de error vuelve al paso anterior.
6. Cuando las pruebas locales son exitosas se procede al despliegue en
   producción del nuevo código. La tarea se marca como lista.
7. Se solicita a equipo que valide la nueva funcionalidad en el contexto real.
   En caso de no satisfacer la necesidad se reabre la tarea y ésta vuelve al
   paso 4.
8. Si la validación es exitosa, esa misma persona cierra la tarea.
9. Se solicita al equipo de QA que realice sus validaciones en busca de fallos
   inadvertidos (no graficado; sólo requerido en cambios visuales o de
   comportamiento de cara al usuario).

Tipos de tarea y prioridad
--------------------------

Los tipos de tarea que habían eran muy diversos. Multiplicados por la cantidad
de niveles de prioridad había una cantidad nefasta de *tipos de servicio* [1]_
donde no se tenía en claro qué se esperaba de cada uno:

* Analysis
* Bug
* Configuration
* Documentation
* Epic
* Improvement
* Maintenance
* New feature
* Task
* Sub-task
* Technical debt

Y los niveles de prioridad, en orden decreciente de importancia:

* Blocker
* P1
* P2
* P3
* Waiting/planned
* Backlog

Primeros pasos hacia Kanban
---------------------------

El primer paso fue hacer limpieza sobre los tipos de tarea:

.. container::

   .. image:: /galleries/path-to-kanban-i/issues.jpg
      :class: img-responsive
      :alt: Tipos de tareas y prioridades
      :align: center

El tipo *Analysis* no está asignado a uno en particular porque fue manipulado
con más detalle: basado en el contenido fue movido a uno u otro tipo de tarea.
El resultado final fue este conjunto reducido de tipos:

* Bug
* Epic
* New feature
* Task
* Sub-task
* Technical debt

No está en la foto, pero las proridades también sufrieron cambios, aunque no
mucho:

* Blocker
* P1
* P2
* P3
* Backlog

Siguentes pasos
---------------

.. raw:: html

   <strike>
     Ser&aacute; determinar el nivel de servicio esperado para cada
     combinaci&oacute;n (coherente) de tipos y prioridades.
   </strike>

Crear una pizarra Kanban para reflejar los estados y obtener estadísticas
iniciales.

`Ir al siguiente post: Camino a Kanban II <http://ariel17.com.ar/posts/path-to-kanban-ii/>`_

.. |kanban| replace:: "Kanban: Successful Evolutionary Change for Your Technology Business"
.. _Kanban: http://www.goodreads.com/book/show/8086552-kanban
.. |Anderson| replace:: David J. Anderson
.. _Anderson: http://www.goodreads.com/author/show/82481.David_J_Anderson
.. _Movile: https://www.movile.com/
.. [1] Acuerdo de Nivel de Servicio (ANS): https://es.wikipedia.org/wiki/Acuerdo_de_nivel_de_servicio
