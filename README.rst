================
django-jsgettext
================

It is an improvement to the implementation of django gettext for javascrtipt.

Features:
---------

- Now parse javascript teplates for translatable strings (tested with underscore.template)
- New I18n view more extensible (build on top of CBV) that exposes the djangojs gettext domain
  and djsgettext domain generated for translation scrings from js templates. Aditionaly the performance is
  improved with caching of this view (not supported by django view as default behavior).


How it works?
-------------

Django ``makemesages`` command generates a djangojs domain po file from ``*.js`` files, ``django-jsgettext``
generates a djgettext domain po file from ``.html`` files (javascript templates) and a new django view
exposes the two gettext domains to the javascript.

.. note::
    The new view is created because the primary view of django is monolitic and not permits expose domains distinct than ``djangojs`` and ``django``.

Currently, only is tested with underscore templates. Example:

.. code-block:: html

    <div><%= gettext('sample message') %></div>
    <div><%= ngettext('1 message', 'some messages', num) %></div>
    <div><%= interpolate(gettext('sample %s'), [1]) %></div>


How use it?
-----------

Add the the I18N view to your urls.py. This view outputs javascript
code with the translations:

.. code-block:: python

    from djsgettext.views import I18n

    urlpatterns = patterns('',
        url(r'^js-gettext/$', I18n.as_view(), name="jsgettext"),
    )

In your html code, include the javascript code that will bring
the translations (this will include both djangojs and
djangogettext catalogs, so it replaces django javascript gettext
script):

.. code-block:: html

   <script type="text/javascript" src="{% url jsgettext %}"></script>

Now you can use function gettext, ngettext (for plurals),
pgettext (for context), npgettext (for plurals with context) in
your templates and javascript files.

Collect messages from templates:

.. code-block:: console

    python manage.py jsgettext_makemessages

Then compile messages as usual:

.. code-block:: console

    python manage.py compilemessages

Also note that the I18N view will be cached by default for 20
seconds. To avoid caching in debug mode, you can use the
DummyCache in settings.py:

.. code-block:: python

    # No caching by default for development
    if DEBUG:
        CACHES = {
            'default': {
                'BACKEND': 'django.core.cache.backends.dummy.DummyCache',
            }
        }

And you can modify the time the view is cached setting 
I18N_VIEW_CACHE_TIMEOUT  in settings.py (in seconds):

.. code-block:: python

    # cache i18n for 15 mins
    I18N_VIEW_CACHE_TIMEOUT = 60*15
