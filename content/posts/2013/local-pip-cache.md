---
date: 2013-03-11 22:47:00+00:00
title: Using a local cache for pip packages
slug: local-pip-cache
tags: [python]
---

I do a lot of development without an internet connection[^1], so
being able to install packages into a virtual environment without a
connection to PyPI is pretty useful.

<!-- more -->

I've got a couple of aliases in my `.bashrc` which help with
this[^2]:

```
alias pipcache='pip install --download ${HOME}/.pip-packages'
alias pipinstall='pip install --no-index --find-links=file://${HOME}/.pip-packages/'
```

The first downloads the packages to my local cache, the second installs them from the cache.

Usage is probably fairly obvious:

```
pipcache Django==1.5 # Put Django-1.5.tar.gz in ~/.pip-packages
pipinstall Django==1.5 # Install ~/.pip-packages/Django-1.5.tar.gz

pipcache -r requirements.txt # Cache all the requirements of a project
pipinstall -r requirements.txt # Install all requirements from the cache
```

It's perfectly possible to cache multiple versions of the same
package[^3], which is useful for being able to test upgrading to
newer releases, whilst still being able to revert to the previously
pinned package if you find nothing works.

Since I mostly use a fairly small set of packages, this means I can
start up a brand new virtualenv and I'm very likely to have
everything I need without going to PyPI. Not having to download large
packages makes installs quicker too[^4].

[^1]: Mostly on the train to and from work.

[^2]: Ideas taken from the Cookbook in the pip documentation.

[^3]: At the time of writing I have three versions of Django locally.

[^4]: Django 1.5 weighs in at around 8MB, and downloading it is
      sometimes surprisingly slow.
