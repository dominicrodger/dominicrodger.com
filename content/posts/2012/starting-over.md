---
date: 2012-04-07 06:58:00+00:00
title: Starting Over
tags: [django, learning]
---

Every once in a while, I want to start again.

I currently have 6 different sites using Kaléo, a bit of software I
wrote for managing Church websites, which I'll write about another
time. The code is stored in a private git repository, and synced out
to 6 different places every time I need to make a change.

<!-- more -->

Most these changes are fairly simple, and upgrading each site looks
roughly like this:

```
source myvirtualenv/bin/activate
git pull
python manage.py migrate
python manage.py collectstatic
apache2/bin/restart
# profit!
```

Since this is all eminently scriptable, it's scripted, and I've got a
quick Python script on my desktop that SSHs to each site in turn, and
updates them.

The problem is, not every upgrade is that simple[^1]. In practice, I
end up having to run a few different commands - generally when I want
to install new dependencies[^2].


## What I should have done


Kaléo is built with [Django](https://www.djangoproject.com/), which
has a really nice system for [dealing with multiple
sites](https://docs.djangoproject.com/en/dev/ref/contrib/sites/).

I should have used that, but back [when I
started](http://stackoverflow.com/questions/744866/reverse-not-found-sending-request-context-in-from-templates)
in 2009 I was just building something for my Church (and learning
Django at the same time), and it didn't occur to me that I might want
to re-use it.


## What I want to do


So now I have 6 different installations of Kaléo, and a maintenance
headache every time I want to make a significant change
(e.g. switching from Python 2.6 to Python 2.7, as I did recently).

This maintenance headache makes me want to just throw it all away and
start again. I'd make it leaner - Kaléo's full of cruft that seemed
like a good idea at the time. I'd use the sites framework, so I could
more easily manage upgrades, and be able to spin up new sites
quicker. I'd probably separate out parts of it into apps, so other
people could use just the parts of it they need. I'd write better
tests.


## Why I don't


But then I remember that most of this code just works, and there's a
better way to do it. I can add tests for what I've already got. I can
gradually pull apart the dependencies I've got, and spin off parts of
it into separate places. I can get to where I want eventually, piece
by piece.


> Programmers are, in their hearts, architects, and the first thing
> they want to do when they get to a site is to bulldoze the place
> flat and build something grand. We're not excited by incremental
> renovation: tinkering, improving, planting flower beds.

— [Joel Spolsky: "Things you should never do"](http://www.joelonsoftware.com/articles/fog0000000069.html)

So tiny step by tiny step I'm getting my code to a better place, tiny
refactor by tiny refactor, and I'm doing it all without breaking
anything (much).

[^1]: This is where I should probably be using something like
      [Fabric](http://fabfile.org), but I haven't got around to it
      yet.

[^2]: There's probably a better way of doing this - I'm using `pip`
      and have a `requirements.txt` file, but I don't want to
      re-install everything, I just want to install the things I
      haven't got installed.
