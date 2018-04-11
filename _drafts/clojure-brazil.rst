.. title: Clojure @ Brasil
.. slug: clojure-brazil
.. date: 2015-09-14 16:54:21 UTC-03:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Desde el Lunes 24/8 al Miércoles 26/8 inclusive estuve en Brasil participando
de un taller de programación en el |tryp|_. 

.. image:: https://pbs.twimg.com/media/CNYJpKBWUAESvwD.jpg:large

El evento fue organizado por Movile_ como patrocinador y por el equipo |ln|_
como catedráticos. Fueron 3 días de 9 a 18 hs con un cronograma indefinido pero
responsivo a las dudas a medida que se presentaban las características básicas
del lenguaje.

Entorno de trabajo
==================

Obviamente, ViM_ :). Mi entorno lo preparé siguiendo este |post|_ acerca de los
mejorcitos plugins. A saber:

* vim-fireplace_
* vim-salve_
* paredit.vim_
* vim-sourround_
* vim-eastwood_
* vim-cljfmt_

Ejercicios
==========

Durante la última clase se aplicaron los conocimientos adquiridos sobre 2
proyectos a modo de ejercicios:

* |tron|_
* |turtle|_

¿Por qué Clojure y no Python?
=============================

Fue una de las preguntas que más sonaban en mi cabeza. *map, reduce,
lazy evaluation, lambda functions*, por poner ciertos ejemplos, fueron
presentados como piezas fundamentales de Clojure y se encuentran en el
repertorio de instrucciones nativas de Python. Entonces: ¿por qué no Python?

La respuesta corta es: **manejo de memoria**. Clojure es un lenguaje que corre
sobre la JVM y Python mantiene su limitación de 1 thread por procesador a
través de GIL_.

Para más detalles sobre comparativas de estos lenguajes se puede consultar
|tabla|_.

Pero dejo la pregunta en el aire para más adelante: **¿Por qué no Jython?**.

.. |tryp| replace:: hotel Tryp de la ciudad de Campinas
.. _tryp: http://www.melia.com/en/hotels/brazil/campinas/tryp-sao-paulo-campinas-hotel/index.html?utm_medium=textlink&utm_source=tripadvisor&utm_campaign=businesslisting_ficha&utm_content=6760
.. _Movile: https://www.movile.com/
.. |ln| replace:: Lambda Next
.. _ln: http://lambdanext.eu/
.. |tron| replace:: Movile Tron
.. _tron: https://github.com/lambdanext/movile-tron
.. |turtle| replace:: Movile Turtle
.. _turtle: https://github.com/lambdanext/movile-turtle
.. _ViM: http://www.vim.org/ 
.. |post| replace:: excelente post
.. _post: http://blog.venanti.us/clojure-vim/
.. _GIL: https://wiki.python.org/moin/GlobalInterpreterLock
.. |tabla| replace:: estas tablas
.. _tabla: http://benchmarksgame.alioth.debian.org/u64q/compare.php?lang=clojure&lang2=python3#faster-programs-measurements
.. _vim-fireplace: https://github.com/tpope/vim-fireplace
.. _vim-salve: https://github.com/tpope/vim-salve
.. _paredit.vim: https://github.com/vim-scripts/paredit.vim
.. _vim-sourround: https://github.com/tpope/vim-surround
.. _vim-eastwood: https://github.com/venantius/vim-eastwood
.. _vim-cljfmt: https://github.com/venantius/vim-cljfmt

