---
date: 2012-06-10 06:11:00+00:00
title: Faster Django Tests
tags: [django, testing]
---

A [long while
ago](http://www.dominicrodger.com/tdd-django-south.html), I
discovered that running Django tests is much faster if you use
SQLite, and if you turn off South (this now seems pretty obvious, but
at the time was a bit of a revelation to me).

<!-- more -->

Since then, I've come across a better way of setting this up, to avoid having a `test_settings.py`:

```
# If manage.py test was called, use SQLite
import sys
if 'test' in sys.argv:
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': j('test_sqlite.db')
        }
    }
```

That pretty well does what my old approach with `test_settings.py`,
but instead of having to type:

```
python manage.py test --settings=test_settings
```

I can now just type:

```
python manage.py test
```

and the faster settings get loaded.

This morning I was perusing [commits to Django on
GitHub](https://github.com/django/django/commits/master)[1. This is
probably something everyone who works with Django should do, it's a
great way of finding out what's coming up, and also for stumbling
across random tips like the one I've not quite got to yet.], when I
came across [this
one](https://github.com/django/django/commit/17d6cd90299e39823e80a005e7a04bc24ee8af4c):


> Documented that setting `PASSWORD_HASHERS` can speed up tests.

Turns out there's another way to speed up tests if you're
authenticating users. Now my extra settings that I set if I'm running
tests look like this:

```
# If manage.py test was called, use SQLite
import sys
if 'test' in sys.argv:
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': j('test_sqlite.db')
        }
    }

    PASSWORD_HASHERS = (
        'django.contrib.auth.hashers.MD5PasswordHasher',
        'django.contrib.auth.hashers.SHA1PasswordHasher',
    )
```

Doing that took a sample run of the tests for my [new
project](https://github.com/dominicrodger/kanisa) down from 3.6s to
1.0s, which is pretty good for 3 lines of code.

Django uses the first entry in the list for storing passwords, and
the other entries in the list are also available. If you want
Django's test suite to pass, you'll need to include the SHA1 hasher.
