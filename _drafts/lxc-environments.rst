.. title: Creando ambientes con Contenedores Linux
.. slug: lxc-environments
.. date: 2014/08/02 10:07:23
.. tags: linux, desarrollo, virtualización
.. link: 
.. description: Crear ambientes de desarrollo usando LXC
.. type: text

Gracias a las herramientas de virtualización es muy fácil hoy en día crear
rápidamente ambientes compuestos por múltiples instancias. VMWare_ o
VirtualBox_ son buenas opciones cuando el sistema operativo a ejecutar en la
máquina virtual es distinto al del sistema anfitrión, pero a la hora de
virtualizar un Linux sobre otro Linux el abanico de opciones se abre hacia
otras opciones más *baratas* en cuanto al consumo de recursos.

Linux Containers
================

Si el ambiente que deseamos crear (o replicar) consiste sólo de instancias
Linux existe una herramienta llamada LXC_: Linux Containers. No es una máquina
virtual sino que provee un *entorno* virtual, separando espacios de procesos
y redes, funcionalidad que provee el propio núcleo de Linux desde la versión
2.6.29 con la integración de cgroups_. Sin la sobrecarga de simular una máquina
virtual por completo hay más recursos disponibles para ser aprovechados.

Pueden obtener más información en la `página oficial de LXC`_.

Instalación
-----------

Podemos instalar LXC directamente desde los repositorios de software de la
distribución que estemos usando:

.. code-block:: bash

   ~$ sudo apt-get install lxc  # for Debian-based distros

También podemos instalar la versión *bleeding edge* directamente desde el
`repositorio de GitHub`_:

.. code-block:: bash

   ~$ sudo apt-get install git  # if you don't have installed git yet
   ~$ git clone git@github.com:lxc/lxc.git
   ~$ sudo su
   #$ cd lxc && ./autogen.sh && ./configure && make && sudo make install

Creación de intancias
---------------------

La creación de containers puede realizarse mediante los templates o indicar
manualmente qué imagen utilizar.

.. code-block:: bash

   ~$ lxc-create -n ubuntu1 -t ubuntu  # creates a container named ubuntu1 using the ubuntu template
   ~$ lxc-create -n ubuntu2 -t ubuntu-cloud -- -r trusty -T http://cloud-images.ubuntu.com/trusty/current/trusty-server-cloudimg-arm64-root.tar.gz

De esta manera, la creación de los containers se realiza sin más intervención
de parte del usuario. También existe una manera de crearlos de manera
interactiva, visualizando las distribuciones y arquitecturas disponibles:

.. code-block:: bash

   ~$ lxc-create -t download -n ubuntu3

Visualizar las instancias creadas
---------------------------------

Para ver las instancias disponibles se puede hacer uso del comando
:code:`lxc-ls`. Este mostrará sólo los nombres pero usando el parámetro
:code:`--fancy` flag agregará más información útil sobre ellas:

.. code-block:: bash

   ~$ sudo lxc-ls --fancy

   NAME     STATE    IPV4        IPV6  AUTOSTART
   ---------------------------------------------
   ubuntu1  RUNNING  10.0.3.228  -     NO
   ubuntu2  RUNNING  10.0.3.147  -     NO
   ubuntu3  STOPPED  -           -     NO

Acceder a una instancia
-----------------------

Obtener acceso a la terminal de una instancia puede realizarse con el comando
:code:`lxc-console -n <instance-name>`. Ejecutar este comando es equivalente a
obtener el acceso a una terminal física del host; **no una conexión remota**.
Para poder cerrar la conexión a esta terminal basta con ingresar
:code:`Ctrl-a+q`.

.. code-block:: bash

   ~$ sudo lxc-console -n db  # enter Ctrl-a+q to close the session

Una consola web para controlar a todos
======================================

Existe un proyecto llamado `LXC Web Panel`_ (un `fork del proyecto original`_
para versiones de lxc>=1.0) que es justamente un sito web para la
administración de los contenedores Linux. Ofrece además de las herramientas de
control, reportes estadísiticos para la visualización del consumo de recursos
tanto del host como de los contenedores.

Instalando Web Panel
--------------------

Para instalar el panel web debemos agregar el paquete a nuestro repositorio
local, instalarlo y cambiar las configuraciones por defecto si así lo
consideramos necesario:

.. code-block:: bash

   ~$ wget -O - http://claudyus.github.io/LXC-Web-Panel/claudyus.gpg.key | sudo apt-key add -
   ~$ echo "deb http://claudyus.github.io/LXC-Web-Panel/ ./" | sudo tee /etc/apt/sources.list.d/lwp.list
   ~$ sudo apt-get update
   ~$ sudo apt-get install lwp
   ~$ sudo cp /etc/lwp/lwp.example.conf /etc/lwp/lwp.conf  # using default configuration
   ~$ sudo service lwp start

Voy a asumir que se usaron las configuraciones por defecto; si realizaron
cambios no olviden adaptar los ejemplos de aquí a sus casos particulares. Una
vez terminada la instalación, abrimos un browser y nos dirigimos a la dirección
web http://localhost:5000/:

.. class:: thumbnail
.. figure:: /galleries/lxc-for-development/login.png
   :width: 100 %
   :scale: 80 %
   :alt: Login en LXC Web Panel.
   :align: center

Usando :code:`admin` como usuario y contraseña por defecto, accederemos al
panel y nos mostrará una visión de los recursos utilizados tanto por el host
como por los contenedores, así como links a configuraciones de red y de las
instancias:

.. class:: thumbnail
.. figure:: /galleries/lxc-for-development/dashboard.png
   :width: 100 %
   :scale: 80 %
   :alt: Dashboard en LXC Web Panel.
   :align: center

Accediendo a alguna de las instancias listadas en la región izquierda veremos
la información asociada a la misma, tal como el estado de ejecución,
propiedades de red, cantidad de procesadores, límites de memoria, ubicación de
la imagen, etc.
   
.. class:: thumbnail
.. figure:: /galleries/lxc-for-development/instance.png
   :width: 100 %
   :scale: 80 %
   :alt: Dashboard en LXC Web Panel.
   :align: center

Armando un esquema propio
=========================

Recomiendo leer el post `Exploring LXC Networking`_ donde se ejemplifica de
manera profunda cada combinación de configuración según el escenario que se
quiera establecer, como conectividad al exterior y/o entre nodos y utilización
de interfaces existentes.

Otras tecnologías similares
===========================

* OpenVZ_
* Linux-VServer_
* Docker_ (basado en LXC)

.. _VMWare: http://www.vmware.com/ 
.. _VirtualBox: https://www.virtualbox.org/ 
.. _OpenVZ: http://openvz.org/
.. _Linux-VServer: http://linux-vserver.org/ 
.. _LXC: https://linuxcontainers.org/
.. _Docker: https://www.docker.com/ 
.. _cgroups: https://www.kernel.org/doc/Documentation/cgroups/cgroups.txt
.. _`página oficial de LXC`: LXC_
.. _`repositorio de GitHub`: https://github.com/lxc/lxc
.. _`LXC Web Panel`: http://claudyus.github.io/LXC-Web-Panel/
.. _`fork del proyecto original`: http://lxc-webpanel.github.io/
.. _`página oficial del panel`: `LXC Web Panel`_
.. _`Exploring LXC Networking`: http://containerops.org/2013/11/19/lxc-networking/
