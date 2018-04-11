.. title: Broken properties
.. slug: broken-properties
.. date: 2014/07/12 19:56:51
.. tags: programming, python, django
.. link: http://www.stavros.io/posts/how-replace-django-model-field-property/
.. description: 
.. type: text

Yesterday I was fighting a while during the day trying to replace a model field
in Django_ with **property**. Property is a mechanism in Python_ language to
implement getters and setters for a class field in a transparent way (*in a 
pythonic way* some might say). Here a very simple example:

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


So this way the recently implemented ``Foo`` class would be used:

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

`The official documentation about this functionality`_ can be found easily.


The problem with Django
=======================

Implement this functionality in Django for model fields is not a problem at
all. The problem resides on using the other advantages this framework offers
over the field used for this solution. I'm going to make a simple example with
a Django_ project:

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


The test project was initialized. By default Django_ has configured SQLite_ as
database engine and for this example it is more than enough. I'm going to edit
the ``propapp`` application's models module and implement getter and setter in
a class. The result is very similar to the previous example:

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

So there it is: a field model with its methods. Here is how to use them:

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

Everything is ok til here; much more, everything is the same, no problem so
far. The problems begin when a query on the field is made:

.. code-block:: bash

   >>> Foo.objects.filter(foo='bar')
   Traceback (most recent call last):
   ...
   File "/home/ariel17/brokenprop/env/local/lib/python2.7/site-packages/django/db/models/sql/query.py", line 1283, in names_to_path
     "Choices are: %s" % (name, ", ".join(available)))
   FieldError: Cannot resolve keyword 'foo' into field. Choices are: _foo, id
   >>>

Ok, the field cannot be used as it was supposed to be ``:(``. Suppose that this
cost is worthless for me and I want to continue this way, I don't care that the
field name is not intuitive enough: I want (and I will) put what it requires to
be putted to get to the field name:

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

**The field is not reachable to be used on queries**. The issue `#3148`_ on
Django_'s track system talks about it, but the solution propoused, on which
this post is based, does not provide the expected functionality (`here my
complains`_).

Except, of course, that **I miss something**.

.. _Django: https://www.djangoproject.com/
.. _Python: https://www.python.org/
.. _`The official documentation about this functionality`: https://docs.python.org/2/library/functions.html#property
.. _SQLite: http://www.sqlite.org/
.. _`#3148`: https://code.djangoproject.com/ticket/3148
.. _`here my complains`: https://code.djangoproject.com/ticket/3148#comment:51
