# Django Matomo API Tracking

This django app enables server side traffic tracking. The code is greatly inspired by the [Django Google Analytics](https://github.com/praekeltfoundation/django-google-analytics) app.

## Prerequisites

For this middleware to work you must have the following items configured:

1. A Matomo server to send tracking data to (obviously).
2. A
   [Celery task queue configured for Django](https://docs.celeryq.dev/en/stable/django/first-steps-with-django.html#using-celery-with-django)
   and functional for the middleware to use.  The task queue
   allows tracking data to be sent asynchronously. You may also need to install a broker of some
   kind for Celery to use. (i.e. RabbitMQ, Redis, a database configured with SQLAlchemy, etc.)

If you receive connection errors (such as 111 or other cryptic numbers), these may be due to problems
connecting to the Celery task queue rather than your Matomo server. See the file [example_db_config.md](example_db_config.md)
for an example of how to configure Celery to use a database as a broker instead of installing extra services on a low
traffic site.

## Installation

1. Install ``django-matomo-api-tracking`` from pypi using ``pip install django-matomo-api-tracking``

## Setup / Configuration

1. add ``matomo_api_tracking`` to your ``INSTALLED_APPS`` setting.
2. add a new variable ``MATOMO_API_TRACKING`` to your settings to configure the behaviour of the app:


    MATOMO_API_TRACKING = {
        'url': 'https://your-matomo-server.com/matomo.php',
        'site_id': <your_site_id>,
        # 'ignore_paths': ["/debug/", "/health/"],
        # 'token_auth': "<your auth token>",  # e.g.  "33dc3f2536d3025974cccb4b4d2d98f4",
        # 'ignore_html': True/False
    }

3. Enable the middleware by adding the matomo_api_tracking middleware to the list of enabled middlewares in the settings: 


    MIDDLEWARE = [
        ...
        'matomo_api_tracking.middleware.MatomoApiTrackingMiddleware',
    ]

In the settings part, the `ignore_path` can be used to entirely skip certain
paths from being tracked. If you specify an `token_auth`, the app will also send
the client's IP address (cip parameter). But this is not required.

The `ignore_html` parameter is optional and defaults to `False`. If set to `True`, the middleware will not track
requests with a `text/html` content type or that look like they are returning HTML content (where there
is probably already a Javascript tracking code and manual tracking doubles the same requests).

This was implemented for a legacy site that shared the same URLs between a web application and
an API with the only difference being the accept an/or content-type headers that differentiate
UI and API requests.