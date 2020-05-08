---
date: 2013-05-20 16:50:00+00:00
title: Using code coverage to find bugs
slug: coverage
---

Yesterday, I found two bugs whilst looking at a code coverage report.

I tend to think that shooting for 100% code coverage adds unnecessary
overheard - often the last few percent doesn't give you much benefit,
and takes a disproportionate amount of time to reach, but it's useful
to at least understand why particular lines of code aren't hit by
your tests. Perhaps those lines are dead code, or perhaps - as was
the case for me yesterday - it's because your code is broken.

<!-- more -->


## Bug #1 - overriding the wrong method


The first bug I found yesterday was in my feed generation code for
[tinyblog](https://github.com/dominicrodger/tinyblog/). Because of
the way feed generation works in Django (see the
[docs](https://docs.djangoproject.com/en/dev/ref/contrib/syndication/)
if you're interested), provided you have a single test that hits your
feed URL, you'll probably have covered 100% of your feed generation
code[1. This shows fairly obviously the problem with 100% coverage -
just by executing a `GET` request on my feed URL I've hit 100%
coverage, even though I've no idea if the code's correct. All I know
is that running the code doesn't cause an exception to be raised,
which is valuable, but hardly sufficient to show my code's working
correctly.]. Strangely, my coverage report showed my tests were
failing to hit one method - `copyright`.

Taking a quick look at the base class for my RSS feed class, I
spotted that it contained no reference to a method named `copyright`,
though it did reference a method named `feed_copyright`. Renaming the
method and re-running my tests showed that I now hit the previously
missing lines of code (and more importantly, my feed now contained
the copyright information I'd always intended to be there).

Presumably, that name's always been wrong - having fixed up the name
I also discovered a glaringly obvious bug in my implementation of
`feed_copyright` which meant it would always raise an exception. I
can only assume I copy-pasted from a broken example of how to do
feeds in Django, without checking whether it worked[2. I wish I could
tell you this didn't sound like me, but never mind.].


## Bug #2 - a broken import


More strangely, I was missing a whole chunk of one file[3. Normally
coverage reports in `coverage.py` don't include blank lines.]. Turned
out that one of my templatetags files had a bad import, which Django
silenced, since `TEMPLATE_DEBUG` was set to `False`[4. The lesson
here is to make sure that `DEBUG` and `TEMPLATE_DEBUG` are set to
`True` in unit tests - the tests fail loudly if I set those two
settings in my `test_settings.py`, and show me exactly where the
problem is.]. This bad import would have meant my template tags just
didn't work. Fixing up the bad import raised the coverage of the
module with the broken import to 100%, which was encouraging, though
yet another sign that 100% coverage is not sufficient (I'd clearly
not been checking the output of any of those template tags, or other
tests would have failed).


## Self-evident conclusions


Aiming for 100% code coverage might not be that useful - it doesn't
tell you anything about whether your code works, and is probably more
effort than it's worth on a project rather more serious than my tiny
blogging application.

However, I do think it is worth at least understanding _why_ your
code coverage isn't 100% - take a look at the lines you're missing
and ask yourself these questions:

1. Would you expect your tests to hit this code?
2. What would it take to make sure your tests do hit this code?
3. Is it worth the effort?
