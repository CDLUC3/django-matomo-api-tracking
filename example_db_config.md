In the *settings.py* you may need to add entries like these to work with Celery and
a relational database instead of a dedicated broker like RabbitMQ or Redis, if you've
configured the Django settings file to contain the Celery settings:

```python
MATOMO_SITE_ID = <your_site_id>
MATOMO_AUTH_TOKEN = '<your auth token>'
MATOMO_BASE_URL = 'https://<your-matomo-server.com>'
MATOMO_API_TRACKING = { 'url': f'{MATOMO_BASE_URL}/matomo.php', 'site_id': MATOMO_SITE_ID,
                        'ignore_paths': ["/manage/",
                                         "/home/"],
                        'token_auth': MATOMO_AUTH_TOKEN,
                        'ignore_html': <True or False> }

# Celery Configuration Options
CELERY_TIMEZONE = "America/Los_Angeles"
CELERY_TASK_TRACK_STARTED = True
CELERY_TASK_TIME_LIMIT = 60
CELERY_BROKER_URL = 'sqla+mysql://<user>:<pwd>@<db-server>/<database>'
CELERY_BACKEND = 'db+mysql://<user>:<pwd>@<db-server>/<database>'

CELERY_TASK_RESULT_EXPIRES = 3600 # 1 hour until completed task results are deleted from database

# There may be other useful Celery config options, but these may get you started.
```
