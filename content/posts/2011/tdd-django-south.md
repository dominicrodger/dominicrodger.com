---
date: 2011-01-11 00:00:00+00:00
title: Test-Driven Development with Django & South
tags: [testing]
---

I'd basically given up on attempting test-driven with Django, given
the project I'm currently working on uses models with a lot of
[South](http://south.aeracode.org) migrations. Just building the
database and running the migrations could take a minute or so when
running `manage.py test`, and resetting the database to a clean state
meant the test suite would take several minutes to run.

<!-- more -->

I've had an idea in the back of my mind for a while, and today I finally got around to making it work.


## SQLite


When using SQLite, the test runner doesn't bother to actually hit the
filesystem, it [just does the whole thing in
memory](http://django.readthedocs.org/en/1.3.-/topics/testing.html#the-test-database),
which is a good deal quicker. Previously, I couldn't use SQLite,
because South doesn't like it (since SQLite doesn't support `ALTER
TABLE`).

My realisation was that if I turned off South, I could use SQLite, which I did with this rather hackish file called `test_settings.py`:

```
from settings import *

INSTALLED_APPS = [app for app in INSTALLED_APPS if app != 'south']

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'test_database.db'
    }
}
```

I can then do a quick run of the test suite using:

```
manage.py test --settings=test_settings
```

Obviously, this doesn't run through my South migrations, which I
probably should do from time to time (especially when adding new
migrations). I can still test those with:

```
manage.py test
```

since by default the test runner will hit my normal settings module,
which still has South in `INSTALLED_APPS`, and which hits my MySQL
database.


## The Results


With my `test_settings`, I get the following output:


> Ran 121 tests in 3.218s


With my default settings, I get:


> Ran 121 tests in 326.742s


If I run tests for a particular app (which is generally all I need to
do), that difference is 0.312s to 31.064s.

That makes my test run approximately **99% quicker**, and well within
what I consider an acceptable time to run every time I make small
changes to my code.
