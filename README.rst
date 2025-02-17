.. image:: https://github.com/KUWAITNET/django-shorter/actions/workflows/tox-tests.yml/badge.svg
   :alt: Python package build status

.. image:: https://github.com/KUWAITNET/django-shorter/actions/workflows/codeql-analysis.yml/badge.svg
   :alt: CodeQL Analysis

.. image:: https://github.com/KUWAITNET/django-shorter/blob/master/coverage_badge.svg
   :alt: Code Coverage
================

A Django application that adds an URL shortener to your site similar to bit.ly.

This is a fork of [django-tinylinks](https://github.com/bitmazk/django-tinylinks).

This project adds a REST API and integration with the [Piwik](http://piwik.org/) Open Analytics
Platform.


Installation
------------

You need to install the following prerequisites in order to use this app::

    pip install Django==2.2
    pip install urllib3==1.25.1
    pip install djangorestframework==3.9.2


If you want to install the latest stable release from PyPi::

    $ pip install TODO

Add ``tinylinks`` to your ``INSTALLED_APPS``::

    INSTALLED_APPS = [
        ...,
        'tinylinks',
    ]

Add the ``tinylinks`` URLs to your ``urls.py``::

    urlpatterns = [
        ...
        re_path(r'^s/', include('tinylinks.urls')),
    ]

Don't forget to migrate your database::

    ./manage.py migrate tinylinks

Settings
--------

TINYLINK_LENGTH
+++++++++++++++

Default: 6

Integer representing the number of characters for your tinylinks. This setting
is used when the app suggests a new tinylink. Regardless of this setting users
will be able to create custom tinylinks with up to 32 characters.


TINYLINK_CHECK_INTERVAL
+++++++++++++++++++++++

Default: 10

Number of minutes between two runs of the check command. This number should be
big enough so that one run can complete before the next run is scheduled.

TINYLINK_CHECK_PERIOD
+++++++++++++++++++++

Default: 300

Number of minutes in which all URLs should have been updated at least
once. If this is 300 it means that within 5 hours we want to update all URLs.

If ``TINYLINK_CHECK_INTERVAL`` is 10 it means that we will run the command
every 10 minutes. Combined with a total time of 300 minutes, this means that we
can execute the command 300/10=30 times during one period.

Now we can devide the total number of URLs by 30 and on each run we will
update the X most recent URLs. After 10 runs, we will have updated all URLs.

TINYLINK_PAGINATE_BY
+++++++++++++++++++++

Default: 10

Number of items to display on the list page '/s/list'.

PIWIK_ID
++++++++

Default: None

The Piwik ID for the of the website in which this app is installed.
This should be easily found on the Settings page under the Websites menu.

PIWIK_URL
+++++++++

Default: None

This is the URL at which your copy of Piwik is running.

PIWIK_TOKEN
+++++++++++

Default: None

The API key provided by Piwik.

GEOIP_PATH
++++++++++

Default: None

The path for the MaxMind GeoIP data.

TINYLINK_VALIDATION_ENABLED
+++++++++++++++++++++

Default: False

The validation of the long url , disabled by default

Usage
-----

Just visit the root URL of the app. Let's assume you hooked the app into your
``urls.py`` at `s/`, then visit `example.com/s/`. You will see your tinylist
overview. Go to `example.com/s/create/` to see a form to submit a new long URL.

After submitting, you will be redirected to a new page which shows the
generated short URL. If you want this URL to have a different short URL, just
change the short URL to your liking.

Now visit `example.com/s/yourshorturl` and you will be redirected to your long
URL.

Piwik Integration
-----------------

If you want to export the data to Piwik, you will have to own a clean
installation of it, so go and download it from (piwik.org)[http://piwik.org/]
and then follow the (installation
guide)[http://piwik.org/docs/installation-maintenance/].

API Resources
-------------

The API is created using django rest framework and it has 6 resources at the
moment.


Tinylinks
+++++++++

``/api/tinylinks/``

The API allows you to retrievce, create, delete and update your tinylinks.

Creating and modifying tinylinks requires authentication and a valid csrf token.

DEFINITION:

    GET http://example.com/s/api/tinylinks/{TINYLINK_ID}/

EXAMPLE REQUEST:

    curl http://example.com/s/api/tinylinks/{TINYLINK_ID}/


DEFINITION:

    POST http://example.com/s/api/tinylinks/

EXAMPLE REQUEST:

    curl -X POST http://example.com/s/api/tinylinks/ -u user:pass -d "long_url=http://google.com/&short_url=goog"


DEFINITION:

    PUT http://example.com/s/api/tinylinks/{TINYLINK_ID}/

EXAMPLE REQUEST:

    curl -X PUT http://example.com/s/api/tinylinks/{TINYLINK_ID}/ -u user:pass -d "long_url=http://google.com/&short_url=g"


DEFINITION:

    PATCH http://example.com/s/api/tinylinks/{TINYLINK_ID}/

EXAMPLE REQUEST:

    curl -X PATCH http://example.com/s/api/tinylinks/{TINYLINK_ID}/ -u user:pass -d "short_url=g"


DEFINITION:

    DELETE http://example.com/s/api/tinylinks/{TINYLINK_ID}/

EXAMPLE REQUEST:

    curl http://example.com/s/api/tinylinks/{TINYLINK_ID}/ -u user:pass


Users
+++++

``/api/users/``

This resource exposes information about users.

DEFINITION:

    GET http://example.com/s/api/users/{USER_ID}/

EXAMPLE REQUEST:

    curl http://example.com/s/api/users/{USER_ID}/


Database statistics
+++++++++++++++++++

``/api/db-stats/``

Retrieve general information about the links stored in the database.
Offers a simple way to acces the total number of links and the total number of
clicks.

DEFINITION:

    GET http://example.com/s/api/db-stats/

EXAMPLE REQUEST:

    curl http://example.com/s/api/db-stats/


Statistics
++++++++++

``/api/stats/``

Retrieve a list of statistics for every tinylinks object in the database.

Query Paramanters:

* paginate_by
* page

DEFINITION:

    GET http://example.com/s/api/stats/

EXAMPLE REQUEST:

    curl http://example.com/s/api/stats/


Tinylink statistics
+++++++++++++++++++

``/api/url-stats/``

Retrieve statistics for individual tinylink objects.

Query Parameters:

* short_url

DEFINITION:

    GET http://example.com/s/api/url-stats/{SHORT_URL}/

EXAMPLE REQUEST:

    curl http://example.com/s/api/url-stats/{SHORT_URL}/

Expanding tinylinks
+++++++++++++++++++

``/api/expand/``

Expand the short link into the long link.

Query Parameters:

* short_url

DEFINITION:

    GET http://example.com/s/api/expand/{SHORT_URL}/

EXAMPLE REQUEST:

    curl http://example.com/s/api/expand{SHORT_URL}/

Contribute
----------

If you want to contribute to this project, please perform the following steps::

    # Fork this repository
    # Clone your fork
    $ mkvirtualenv -p python3.6 django-tinylinks
    $ pip install -r requirements.txt
    $ ./manage.py test
    # You should get no failing tests
    $ Run coverage to generate a .coverage file, then run
    # coverage json
    $ To generate a json file, and then run
    # ./badgegen.py
    $ To generate the badge
    $ git co -b feature_branch master
    # Implement your feature and tests
    $ ./manage.py test
    # You should still get no failing tests
    # Describe your change in the CHANGELOG.txt
    $ git add . && git commit
    $ git push origin feature_branch
    # Send us a pull request for your feature branch

If you are making changes that need to be tested in a browser (i.e. to the
CSS or JS files), you might want to setup a Django project, follow the
installation insttructions above, then run ``python setup.py develop``. This
will just place an egg-link to your cloned fork in your project's virtualenv.

Roadmap
-------

Check the issue tracker on github for milestones and features to come.
