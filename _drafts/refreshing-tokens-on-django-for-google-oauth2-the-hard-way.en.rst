.. title: Refreshing tokens on Django for Google Oauth2, the hard way
.. slug: refreshing-tokens-on-django-for-google-oauth2-the-hard-way
.. date: 2014/10/30 10:00:16
.. tags: google, oauth2, python, social
.. link: 
.. description: Refreshing tokens on Django for Google Oauth2 
.. type: text

When I say *the hard way* I mean that it was SO difficult *to me* to find the
correct settings combination to getting things done on Django_ combining
python-social-auth_ and google-api-python-client_. The scenario was so simple
yet so complicated to put it to work as it should, that I am a little
frustrated.

The scenario
============

Synchronize custom user events from into their Google Calendar, not in both
directions only :code:`Django -> GCalendar`. The user should authenticate the
account to use for synchronization only once, but `Google Oauth2`_ tokens has 1
hour as life time, so they must be refreshed or request for permissions to user
again. None user information should be updated.

Django environment
==================

Google already has the google-api-python-client_ library to easily implement
authentication and authorization flows but I wanted to use a library that has a
more elegant implementation: python-social-auth_. Also this last one enables a
quick way to use `many more backends`_, not only Google and `not only
socials`_. So this is what I got:

.. code-block:: text

   # Requirements
   Django==1.7
   python-social-auth==0.2.1
   google-api-python-client==1.2
   raven==5.0.0  # you *really* should get Sentry! https://getsentry.com/
   requests==2.3.0

And the related :code:`settings.py` configuration:

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
       'access_type': 'offline',  # Enables the refreshing grant
       'approval_promt': 'force'  # Enables refresh_token
   }

   SOCIAL_AUTH_PIPELINE = (  # Removed user creation and profile update
       'social.pipeline.social_auth.social_details',
       'social.pipeline.social_auth.social_uid',
       'social.pipeline.social_auth.auth_allowed',
       'social.pipeline.social_auth.associate_user',
       'social.pipeline.social_auth.load_extra_data',
   )

What does all the trick is the extra argument :code:`approval_promt` because
Google only sends the :code:`refresh_token` when its present. Despite the fact
that one already indicated the offline access type: when the refresh moment
comes one can only do this with that value, none other is valid. **Please,
refute me.**

Synchronizing and refreshing
============================

A custom event model as example with sync + token refreshing on authentication
error:

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

Happy weekend! :)

**UPDATE on 2014-11-11:** python-social-auth_ has updated the documentation
about how to refresh tokens: http://psa.matiasaguirre.net/docs/use_cases.html#re-prompt-google-oauth2-users-to-refresh-the-refresh-token

Today I have found that refresh_tokens also expires, so the correct
``approval_promt`` value to use is ``auto``:

.. code-block:: python

   SOCIAL_AUTH_GOOGLE_OAUTH2_AUTH_EXTRA_ARGUMENTS = {
       'access_type': 'offline',  # Enables the refreshing grant
       'approval_promt': 'auto'  # Enables refresh_token, for ever and ever
   }

.. _Django: https://www.djangoproject.com/
.. _python-social-auth: http://psa.matiasaguirre.net/
.. _google-api-python-client: https://developers.google.com/api-client-library/python/
.. _`Google Oauth2`: https://developers.google.com/accounts/docs/OAuth2
.. _`many more backends`: http://psa.matiasaguirre.net/docs/backends/index.html#social-backends
.. _`not only socials`: http://psa.matiasaguirre.net/docs/backends/index.html#non-social-backends
