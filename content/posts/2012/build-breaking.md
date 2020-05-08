---
date: 2012-04-29 11:27:00+00:00
title: Travis, You Are Awesome
---

I came across [Travis CI](http://travis-ci.org) this week, and it's
awesome.

I've got a bunch of tests for
[django-magazine](https://github.com/dominicrodger/django-magazine),
but I'm not very good at running them. It turns out that unit tests
aren't very useful if you don't run them, so I wanted to make sure
that every time I pushed code to it, the tests run. Enter Travis CI.

<!-- more -->


## Easy Unit Testing for Reusable Apps


django-magazine is a reusable app - that is, it doesn't ship with
settings files, so you can't run its tests just after installing
it[1. This is probably a good time to point out that django-magazine
is not available on PyPI yet. I've never added anything to PyPI
before, and there's a couple of things I'd like to sort out before
doing that. Firstly I'd like to make sure I've tagged things
properly, and secondly I need to factor out the book review stuff
which is currently not tested, and is very specific to my particular
use case.].

I wasn't sure what the easiest way to handle this was, so I looked
around for Django reusable apps that were already using Travis, and
stumbled across
[django-forms-builder](https://github.com/stephenmcd/django-forms-builder). They'd
solved this by adding an `example_project` folder, which had a simple
settings file, a urls file and a `manage.py`[2. They all had a
`templates` folder, with a base template, which django-magazine
doesn't need. That might be because I've done something wrong (every
template extends `magazine/magazine_base.html`, which doesn't have
much in the way of HTML, but enough that all the tests pass — one of
these days I should probably give it some simple styles).].

That allows me to run the tests without any extra set-up, though I
still haven't worked out how to allow people who've just installed
from pip[3.As opposed to `git clone`, which is what Travis CI does,
which means that the `example_project` code is in a predictable
location (also, I've not yet added `example_project` to my manifest
file, so it's not installed by pip).] to run the tests right out of
the box.


## .travis.yml


There are just two things you need to do to get Travis CI working for your project.

Firstly - sign in on their website. There's a slightly scary message
about giving it write access to your repositories, but there's a
decent enough reason on the Travis site explaining why they need
it[4. _"Travis CI needs write access for setting up service hooks for
your repositories when you request it, but it won't touch anything
else."_ — [Getting Started - Travis CI
Documentation](http://about.travis-ci.org/docs/user/getting-started/)I'd
love there to be a workaround for this, but Travis is awesome enough
that I mostly just don't care.].

Secondly - add a .travis.yml file to your root directory, commit, and
push to GitHub. Travis CI will pick up your change, and start
building. [My
file](https://github.com/dominicrodger/django-magazine/blob/28086f124e6752bad9f78466739673871adb5242/.travis.yml)
currently looks like this:

```
language: python
python:
  - "2.6"
  - "2.7"
env:
  - DJANGO=1.3.1
  - DJANGO=1.4
install:
  - pip install -q Django==$DJANGO --use-mirrors
  - pip install -r requirements.txt --use-mirrors
  - pip install pep8
script:
  - pep8 --exclude=migrations magazine
  - ./magazine/example_project/manage.py test magazine
```

This will kick off four builds:

1. Python 2.6 and Django 1.3.1;
2. Python 2.6 and Django 1.4;
3. Python 2.7 and Django 1.3.1;
4. Python 2.7 and Django 1.4.

Each of those will run in parallel, and you'll then get an e-mail
with the status of your build, if it changed (pleasingly, it doesn't
e-mail you for every build - only if the status of it has switched
between fixed and broken). Go take a look at an [example build
result](http://travis-ci.org/#!/dominicrodger/django-magazine/builds/1199479).

The `--use-mirrors` stuff is an attempt to help Travis CI avoid
overloading PyPI.

## Bonus Points: PEP 8

I've recently discovered PEP 8, which I half knew about, but I'd
never attempted to make my code meet it. Then [someone
forked](https://github.com/joshuajonah/django-magazine)
django-magazine to make it meet PEP 8, and I was shamed into fixing
it. I've added a line to my `.travis.yml` file so the build fails if
checked-in code is not PEP 8 friendly.


## Advertise Your Status


Travis CI even provides an image that'll show your current build
status - adding it to my `README.md` file allows me to advertise that
(and implicitly, that I'm a bit obsessive) using:


```
https://secure.travis-ci.org/dominicrodger/django-magazine.png?branch=master
```


Currently, that looks like: [![Build
Status](https://secure.travis-ci.org/dominicrodger/django-magazine.png?branch=master)
](http://travis-ci.org/dominicrodger/django-magazine)

All this is to say, Travis CI is awesome, and you should use it.
