.. title: Renovando tokens en Django para Google Oauth2, de la manera difícil
.. slug: refreshing-tokens-on-django-for-google-oauth2-the-hard-way
.. date: 2014/10/30 10:00:16
.. tags: google, oauth2, python, social
.. link: 
.. description: Renovando tokens en Django para Google Oauth2 
.. type: text

Cuando digo *de la manera difícil* quiero decir que fue MUY difícil *para mí*
encontrar la combinación correcta de configuraciones para tener funcionando
Django_ combinado con python-social-auth_ y google-api-python-client_. El
escenario era tan simple y sin embargo tan complicado de hacerlo funcionar como
debía, que estoy un poco frustrado.

El escenario
============

Sincronizar un evento propio del usuario hacia su cuenta de Google Calendar, no
en ambas direcciones sino sólo :code:`Django -> GCalendar`. El usuario debe
autenticar la cuenta para usar la sincronización sólo una vez, pero los tokens
de `Google Oauth2`_ tienen 1 hora de tiempo de vida, así que tienen que ser
renovados o solicitarle al usuario que autorice la aplicación nuevamente.
Ningún dato del usuario se debe actualizar.

El ambiente Django
==================

Google ya cuenta con la biblioteca google-api-python-client_ para facilitar la
implementación de los flujos de autenticación pero yo quise usar una biblioteca
que tiene una manera más elegante de hacerlo: python-social-auth_. Este úlltimo
también permite hacer uso de `muchos otros backends`_ muy fácilmente, no sólo
Google y `no sólo sociales`_. Así que esto es lo que tengo:

.. code-block:: text

   # Requerimientos
   Django==1.7
   python-social-auth==0.2.1
   google-api-python-client==1.2
   raven==5.0.0  # *realmente* deberia tener Sentry! https://getsentry.com/
   requests==2.3.0

Y las configuraciones relacionadas en :code:`settings.py`:

.. code-block:: python

   # project/settings.py

   AUTHENTICATION_BACKENDS = (
       'social.backends.google.GoogleOAuth2',
       'django.contrib.auth.backends.ModelBackend',
   )

   SOCIAL_AUTH_LOGIN_REDIRECT_URL = '/home/'

   SOCIAL_AUTH_LOGIN_ERROR_URL = SOCIAL_AUTH_LOGIN_REDIRECT_URL

   SOCIAL_AUTH_DISCONNECT_REDIRECT_URL = SOCIAL_AUTH_LOGIN_REDIRECT_URL

   SOCIAL_AUTH_GOOGLE_OAUTH2_SCOPE = [
       'https://www.googleapis.com/auth/calendar',
   ]

   SOCIAL_AUTH_GOOGLE_OAUTH2_AUTH_EXTRA_ARGUMENTS = {
       'access_type': 'offline',  # Activa el permiso de renovación
       'approval_promt': 'force'  # Activa refresh_token
   }

   SOCIAL_AUTH_PIPELINE = (  # Quitado creación de usuario y actualización del perfil
       'social.pipeline.social_auth.social_details',
       'social.pipeline.social_auth.social_uid',
       'social.pipeline.social_auth.auth_allowed',
       'social.pipeline.social_auth.associate_user',
       'social.pipeline.social_auth.load_extra_data',
   )

Lo que hace toda la magia es el argumento extra :code:`approval_promt` porque
Google sólo envía el :code:`refresh_token` cuando está presente. No importa el
hecho de que uno ya indicó el tipo de acceso offline: cuando el momento de la
renovación llegue uno sólo puede hacerlo usando ese valor, ningún otro es
válido. **Por favor, refútenme.**

Sincronizando y renovando
=========================

Un modelo que representa un evento de usario con sincronización + renovación
del token cuando sucede un error de autenticación:

.. code-block:: python

   #!/usr/bin/env python
   # -*- coding: utf-8 -*-

   """
   Model definition for appliation ``app``.
   """

   import logging

   from apiclient.discovery import build
   from oauth2client.client import AccessTokenCredentials, AccessTokenCredentialsError
   from requests.exceptions import HTTPError
   import httplib2

   from django.contrib.auth.models import User
   from django.db import models

   from social.apps.django_app.default.models import UserSocialAuth
   from social.apps.django_app.utils import load_strategy

   from app.models import Event


   logger = logging.getLogger(__name__)


   class CustomEvent(models.Model):
       """
       A very simple custom event model.
       """
       name = models.CharField(max_length=200)
       description = models.CharField(max_length=200)
       user = models.ForeignKey(User)

       def __unicode__(self):
           return u"{0} ({1})".format(self.name, self.pk)

       def get_google_data(self):
           """
           Implementing the Calendar API content required to create an event:
           https://developers.google.com/google-apps/calendar/v3/reference/events?hl=es#resource
           """
           return {}  # dummy of course

       def sync_event(self, retry=True):
           """
           Pushes current event to the user's Google Calendar service, if it is enabled.

           :param retry: Indicates if it should retry on an authentication error.
           :type retry: ``bool``
           :return: Synchronization result from Google API.
           :rtype: ``dict`` or ``None``
           """
           try:
               provider = self.user.social_auth.get(provider='google-oauth2')
           except UserSocialAuth.DoesNotExist:
               logger.debug(u"Google is not authenticated; nothing to do :(")
               return

           logger.debug(u'Syncronizing event %r into Google Calendar for user %r',
                        self, self.user)

           credentials = AccessTokenCredentials(
               provider.extra_data['access_token'], 'my-user-agent/1.0'
           )
           http = httplib2.Http()
           http = credentials.authorize(http)
           service = build(serviceName='calendar', version='v3', http=http)

           data = self.get_google_data(user)
           logger.debug(u'Creating event with data: %r', data)

           try:
               result = service.events().insert(
                   calendarId='primary', body=data
               ).execute()

           except AccessTokenCredentialsError, e:
               result = None
               try:
                   provider.refresh_token(load_strategy())
               except HTTPError, e:
                   provider.delete()
                   logger.error(
                       u"Error trying to refesh token. Authorization object "
                       "was removed.", exc_info=True, extra={
                           u'response': {
                               u'body': e.response.content,
                               u'url': e.response.url,
                               u'status_code': e.response.status_code,
                           },
                           u'request': {
                               u'url': e.response.request.url,
                               u'method': e.response.request.method,
                               u'body': e.response.request.body,
                           },
                       })

           except Exception, e:
               logger.error(
                   u'[sync_event] Unexpected exception synchronizing event',
                   exc_info=True, extra={
                       u'user': {
                           u'pk': self.pk,
                           u'email': self.email,
                       },
                       u'provider': {
                           u'pk': provider.pk,
                           u'extra_data': provider.extra_data,
                       },
                       u'event': {
                           u'pk': self.pk,
                           u'name': self.name,
                       },
                   })

           if retry:
               return self.sync_event(retry=False)

           logger.debug(u'Event creation result: %r', result)
           return result

¡Buen fin de semana! :)

**ACTUALIZACIÓN del 2014-11-11:** python-social-auth_ ha actualizado la
documentación acerca de cómo renovar tokens: http://psa.matiasaguirre.net/docs/use_cases.html#re-prompt-google-oauth2-users-to-refresh-the-refresh-token
 
Hoy encontré que los refresh_tokens también expiran, así que el valor correcto
de ``approval_promt`` a usar es ``auto``:

.. code-block:: python

   SOCIAL_AUTH_GOOGLE_OAUTH2_AUTH_EXTRA_ARGUMENTS = {
       'access_type': 'offline', # Activa el permiso de renovación
       'approval_promt': 'auto'  # Activa refresh_token, por siempre y para siempre
   }

.. _Django: https://www.djangoproject.com/
.. _python-social-auth: http://psa.matiasaguirre.net/
.. _google-api-python-client: https://developers.google.com/api-client-library/python/
.. _`Google Oauth2`: https://developers.google.com/accounts/docs/OAuth2
.. _`muchos otros backends`: http://psa.matiasaguirre.net/docs/backends/index.html#social-backends
.. _`no sólo sociales`: http://psa.matiasaguirre.net/docs/backends/index.html#non-social-backends
