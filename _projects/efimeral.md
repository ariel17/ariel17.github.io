---
layout: page
title: Efimeral
status: Inception
---

![inception](/assets/projects/efimeral.jpg)

# Where is it

* [GitHub](https://github.com/ariel17/efimeral)
* [Web site](http://ariel17.com.ar/efimeral/)
* [API blueprint](http://ariel17.com.ar/efimeral/api.html)

# Descripción

El sistema es un servicio web. Un usuario entrará al sitio web y eligirá un
sistema operativo preconfigurado. La elección lo llevará a una consola web SSH
que se conectará realmente a un servidor dockerizado. El usuario debe ser capaz
de hacer los cambios que se le antojen sobre la instancia, pero no deber ser
capaz de tomar el control de la infrastructura que la contiene. Luego de cierto
tiempo de iniciada la sesión web, un proceso responsable de controlar el tiempo
de la misma, terminará la sesión, destruyendo la instancia y los datos alojados
en ella. Si el usuario abandona la sesión antes de tiempo, el efecto es
virtualmente el anterior ya que no puede retomarse la sessión SSH web desde
donde estaba.

# Status

**WIP**
