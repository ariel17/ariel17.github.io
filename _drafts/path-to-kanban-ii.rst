.. title: Camino a Kanban II
.. slug: path-to-kanban-ii
.. date: 2016-05-09 06:47:58 UTC-03:00
.. tags: management
.. category: 
.. link: 
.. description: 
.. type: text

`Ir al post anterior: Camino a Kanban I <http://ariel17.com.ar/posts/path-to-kanban-i/>`_

Intento #1
==========

Con los datos recopilados en la etapa 1 elaboramos un dashboard donde se
reflejara el flujo planteado:

.. container::

   .. image:: /galleries/path-to-kanban-ii/kanban-i.jpg
      :alt: Primer intento de pizarra Kanban

Se planteó un esquema de colores para las prioridades. En la parte inferior
central se puede ver la definición del mismo donde se plantea:

* **Blanco:** Blocker.
* **Verde:** P1
* **Celeste:** P2
* **Amarillo:** P3
* **Púrpura:** subtareas

Los tickets del backlog eran analizados y una vez colocados en la columna
"Analizados" se utilizaban como base para obtener un subconjunto, según las
prioridades del negocio, el cual iba a formar el siguiente sprint.

El límite de trabajo en curso (WIP) era considerado por prioridad.

Los tickets bloqueados fueron marcados con una cinta de color.

Los tickets que eran cerrados sin atacar o cancelados eran colocados en la
parte inferior derecha (el tacho de basura).

Problemas
---------

En la misma foto se puede apreciar que el esquema de colores fallaba porque *la
prioridad es una variable* (hay tickets de color P1 en la fila de P3): debido a
una ventana de oportunidad en el negocio, el objetivo a mediano plazo puede
convertirse en uno a corto plazo.

Asignar el límite WIP horizontalmente fue un error de concepto: los mismos
deben asignarse por columna.

Intento #2
==========

Habiendo observado los problemas mencionados, se hicieron algunas
modificaciones sobre el tablero:

.. container::

   .. image:: /galleries/path-to-kanban-ii/kanban-ii.jpg
      :class: img-responsive
      :alt: Segundo intento de pizarra Kanban
      :align: center

Límite WIP por columna: valores iniciales se plantearon de forma aleatoria.

Se agregó información sobre los epics_ que están en curso, para ayudar a
obtener una imagen top-down_ del estado del sistema. También se agregó
estadísticas obtenidas mediante sprints pasadas:

* **Lead time:** tiempo promedio de aquellos tickets terminado en cerrarse
  dentro del sprint (desde que se inicia el sprint hasta que llega al estado
  "Validación OK"). No se toman fechas pasadas previas al sprint, aunque un
  ticket haya sobrevivido por más de uno.

* **Throughput:** Cantidad de tickets promedio que se completan en los sprints.
  Este número es un promedio de todos los sprints pasados y funciona como una
  proyección a lo que se debería esperar e incluso mejorar.

Los colores ya no significan nada; la prioridad del ticket está dada por su
posición en el tablero.

Problemas
---------

La columna "Analizado" está colapsada. Ésto no se debe a que los valores WIP
no están funcionando, sino a que ese estado no está considerado dentro del
flujo real del sistema; sólo a partir de la columna "Release". Cualquier avance
que pudiéramos hacer respecto al backlog, *seguía siendo backlog*.

De las epics, no se sabe cuál está en curso y cuál está siendo analizada o en
otro estado.

Intento #3
==========

Se extendió el flujo oficial del equipo para absorver más estados:

.. container::

   .. image:: /galleries/path-to-kanban-ii/kanban-iii.jpg
      :class: img-responsive
      :alt: Segundo intento de pizarra Kanban
      :align: center

Los nuevos (o casi):

* **A analizar**
* **En análisis**
* **Listo para empezar:** la antigua columna "Analizado".
* **Bloqueado:** significa que la tarea está esperando alguna otra de otro
  equipo.

Ahora el sprint parte del backlog. Las prioridades del negocio nos aconsejan
qué tickets deben ser depositados para análisis y en consecuencia son atacados
desde esa etapa como parte misma del desarrollo, evitando avanzar a ciegas
sobre una tarea.

En la foto no se ve, pero se agregó unas columnas similares para los tickets
epics:

* Para empezar
* En curso
* Terminado

Siguientes pasos
================

Trasladar lo reflejado en el pizarrón a herramientas informáticas.

`Ir al post anterior: Camino a Kanban I <http://ariel17.com.ar/posts/path-to-kanban-i/>`_

.. _epics: https://confluence.atlassian.com/agile067/glossary/epic
.. _top-down: https://es.wikipedia.org/wiki/Top-down_y_bottom-up
