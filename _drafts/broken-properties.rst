.. title: Broken properties
.. slug: broken-properties
.. date: 2014/07/12 19:56:51
.. tags: programación, python, django
.. link: http://www.stavros.io/posts/how-replace-django-model-field-property/
.. description: 
.. type: text

Ayer estuve peleando un rato largo durante el día tratando de reemplazar un
campo en un modelo de Django_ con **property**. Property es un mecanismo en el
lenguaje Python_ para implementar getters y setters de un campo en una clase de
manera transparente (*de forma pytónica* dirían algunos). Acá un ejemplo
sencillo:

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-
   
   """
   File: foo.py
   Description: A very simple property implementation on a very simple class.
   """

   import random


   class Foo(object):
       """
       This is a foo class implementation.
       """
       _foo = 1

       @property
       def foo(self):
           """
           Access the _foo field but doing some dummy calculations.
           """
           return random.randint(1, self._foo)

       @foo.setter
       def foo(self, value):
           """
           Setter for _foo field but doing some "validations".
           """          
           if value > 10:
               raise ValueError("foo field cannot be greater than 10")

           self._foo = value


Así se usaría la clase ``Foo`` recién implementada:

.. code-block:: bash

   ~$ python
   >>> from foo import Foo
   >>> f = Foo()
   >>> f._foo  # _foo field is still accessible
   1
   >>> f.foo
   1
   >>> f._foo == f.foo
   True
   >>> f.foo  # since f._foo == 1, random will only return 1
   1
   >>> f.foo = 5
   >>> f.foo  # now f._foo == 5; random numbers will vary between 1 and 5
   3
   >>> f.foo = 20  # this will raise a ValueError exception
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
     File "foo.py", line 31, in foo
       raise ValueError("foo field cannot be greater than 10")
   ValueError: foo field cannot be greater than 10
   >>>

`Acá se puede leer la documentación oficial`_ acerca de esta funcionalidad.


El problema con Django
======================

Implementar esta funcionalidad en Django_ para los campos de un modelo no es un
problema en absoluto. El problema reside en querer utilizar las demás ventajas
que ofrece el framework sobre el campo en el que se utilizó esta solución. Voy
a ejemplificarlo con un proyecto muy simple:

.. code-block:: bash

   ~$ sudo apt-get install python-pip && sudo pip install virtualenv

   ... (skipped output)

   ~$ mkdir brokenprop && cd brokenprop
   ~/brokenprop$ virtualenv env
   
   ... (skipped output)

   (env)~/brokenprop$ pip install django
   
   ... (skipped more output)
   
   (env)~/brokenprop$ django-admin.py startproject brokenprop && cd brokenprop
   (env)~/brokenprop/brokenprop$ chmod +x manage.py && ./manage.py startapp propapp


El proyecto de pruebas ya está inicializado. Por defecto Django_ trae
configurado SQLite_ como base de datos y para este ejemplo es más que
suficiente. Voy a editar el módulo de modelos de la aplicación ``proapp`` e
implementar setter y getter en una clase. El resultado es muy similar al
ejemplo anterior:

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-
   
   """
   File: models.py
   Description: Model implementations for application 'propapp'.
   """

   from django.db import models


   class Foo(models.Model):
       """
       A foo model implementation.
       """ 
       _foo = models.CharField(
           max_length=200,
           blank=True,
           null=True,
           db_column='foo',  # keeps the field name as it should
       )

       @property
       def foo(self):
           """
           The foo getter
           """
           # doing the things that a getter method does ...
           return self._foo

       @foo.setter
       def foo(self, value):
           # doing the things that a setter method does ...
           self._foo = value

Ya tengo el modelo con un campo y sus respectivos métodos. Así debería usarlos:

.. code-block:: bash

   (env)~/brokenprop/brokenprop$ ./manage.py syncdb --noinput

   ... (skipped output for sync stuff) ...

   (env)~/brokenprop/brokenprop$ ./manage.py shell

   >>> from propapp.models import Foo
   >>> f = Foo()
   >>> f.foo = 'a'
   >>> f._foo
   'a'
   >>> f._foo == f.foo
   True

Todo muy bien hasta acá; es más, todo es igual, sin problemas. Los problemas
comienzan cuando se quiere usar el campo en una query:

.. code-block:: bash

   >>> Foo.objects.filter(foo='bar')
   Traceback (most recent call last):
   ...
   File "/home/ariel17/brokenprop/env/local/lib/python2.7/site-packages/django/db/models/sql/query.py", line 1283, in names_to_path
     "Choices are: %s" % (name, ", ".join(available)))
   FieldError: Cannot resolve keyword 'foo' into field. Choices are: _foo, id
   >>>

Bien, no puedo usar el campo tal como lo debería usar ``:(``. Supongamos
que asumo este costo y quiero continuar así, no me importa que el nombre
del campo en las queries no sea el evidente e intuitivo, sino que usaré el
nombre que le puse con el prefijo ``_`` o lo que se requiera para acceder a
él:

.. code-block:: bash

   >>> Foo.objects.filter(_foo='bar')
   Traceback (most recent call last):
   ...
   File "/home/ariel17/brokenprop/env/local/lib/python2.7/site-packages/django/db/backends/sqlite3/base.py", line 451, in execute
     return Database.Cursor.execute(self, query, params)
   OperationalError: no such table: propapp_foo

   >>> Foo.objects.filter(__foo='bar')
   Traceback (most recent call last):
   ...
   File "/home/ariel17/brokenprop/env/local/lib/python2.7/site-packages/django/db/models/sql/query.py", line 1283, in names_to_path
     "Choices are: %s" % (name, ", ".join(available)))
   FieldError: Cannot resolve keyword '' into field. Choices are: _foo, id

   >>> Foo.objects.filter(propapp__foo='bar')
   Traceback (most recent call last):
   ...
   File "/home/ariel17/brokenprop/env/local/lib/python2.7/site-packages/django/db/models/sql/query.py", line 1283, in names_to_path
     "Choices are: %s" % (name, ", ".join(available)))
   FieldError: Cannot resolve keyword 'propapp' into field. Choices are: _foo, id

   >>> Foo.objects.filter(propapp_foo='bar')
   Traceback (most recent call last):
   ...
   File "/home/ariel17/brokenprop/env/local/lib/python2.7/site-packages/django/db/models/sql/query.py", line 1283, in names_to_path
     "Choices are: %s" % (name, ", ".join(available)))
   FieldError: Cannot resolve keyword 'propapp_foo' into field. Choices are: _foo, id

   >>>

**El campo está inaccesible para usarlo en queries**. El issue `#3148`_ del
track de Django_ habla al respecto, pero la solución planteada, en la que está
basada este post, no provee la funcionalidad esperada (`mis quejas aquí`_).

Salvo, claro, que **me esté perdiendo algo**.

.. _Django: https://www.djangoproject.com/
.. _Python: https://www.python.org/
.. _`Acá se puede leer la documentación oficial`: https://docs.python.org/2/library/functions.html#property
.. _SQLite: http://www.sqlite.org/
.. _`#3148`: https://code.djangoproject.com/ticket/3148
.. _`mis quejas aquí`: https://code.djangoproject.com/ticket/3148#comment:51
