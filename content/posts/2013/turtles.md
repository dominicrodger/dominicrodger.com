---
date: 2013-05-01 19:19:00+00:00
title: Turtles all the way down
slug: turtles
---

One of the programming lessons I find myself re-learning again and
again is this:


> It's turtles all the way down [^1].


When I first started my current job, just after graduation, I was
working in a frontend development rôle. The app I was working on
involved storing customer data in a proprietary database system, and
from time to time, I'd get stuck.

<!-- more -->

Perhaps data wasn't being saved in the way I expected, or perhaps
operations on the database were slow in ways I didn't expect. I'd
fire up the debugger, and start stepping through the code. And then
I'd stop.

I'd stop as soon as the debugger reached code I didn't own - as soon
as I landed up in the database code. I'd find myself in an unfamiliar
land without a map, so I'd give up, and get help - normally from
someone in the database team.

Time marched on, I moved in to the database team, and gradually grew
familiar with the codebase. I realised that actually, the differences
between the code I'd been working on in the app team and the code I
was working on in the database team weren't that great. The borders
I'd crossed in the debugger started to seem less significant - it's
just code, and I've got good enough at this to figure it out. It
doesn't matter that I didn't write it, someone not too different to
me did, and with a bit of work I can work out what they were trying
to do.

Now, my borders are a bit further down - in the land of filesystem
interactions or network calls, but there are still layers below me,
and they were written by people just like me[^2], so perhaps if I
keep my cool, I might be able to figure out what's going on.

The same's true with the web development platforms I use. I write my
websites in Django, and there's no need for me to quit `gdb` once I'm
in Django's internals - they were written by someone like
me[^3]. Chances are, the
bug's in my code (because hey, Django's perfect), but stepping into
Django's internals can help me figure out if I'm using them wrong, or
if I've misunderstood how they're meant to behave. One time in a
thousand, I might even find a bug in Django[^4].

The layers below the code you wrote may not have been written by you, but that doesn't mean you have to stop the debugger or step over the functions the layer beneath your code whilst you work.

Those layers were written by someone just like you, and they aren't
magic - they're just turtles all the way down.

[^1]: The origin of this phrase is somewhat mysterious - see
      Wikipedia.

[^2]: Except probably with awesome neckbeards.

[^3]: Except probably rather better at Python.

[^4]: This has actually never happened to me, though I did once find
      a [typo in the
      documentation](https://github.com/django/django/commit/0f6555bce2),
      which I'm extraordinarily proud of.
