---
date: 2014-06-14 20:52:00+00:00
title: Prototyping with staticjinja
slug: staticjinja
---

I've started building a website for a friend of mine, who works for
an organisation called [Talitha](http://www.talitha.org.uk).

I wanted to get something up and running quickly (since I figured _a_
website was better than no website), so I just started playing with
[Bootstrap](http://www.getbootstrap.com). From there, I had an idea
of what I wanted the site to look like, and all was well. I threw up
a single page site that introduced the organisation a little bit.

Problem is, then I had to make a second page.

<!-- more -->

I didn't want to repeat myself (because I'm obsessive like that), and
I didn't feel like setting something up with
[Pelican](http://www.getpelican.com).

I really just wanted to use Jinja, and extend a base template
somewhere. I started looking for a library that would let me do that,
and came across
[staticjinja](http://staticjinja.readthedocs.org/en/latest/).

It didn't quite do what I wanted, so I wrote [a few
patches](https://github.com/Ceasar/staticjinja/commits?author=dominicrodger)
for it:



1. Added support for static files (which now get copied from a source
   directory to an output directory).
2. Added a standalone build script (called `staticjinja`) to avoid
   needing any custom Python script to build my site.

All this (which took about a week of train journeys...) meant I could
write that second page, without ever repeating myself.

It took a lot longer than just copying and pasting that first page,
but I've never been one to shy away from [yak
shaving](http://www.hanselman.com/blog/YakShavingDefinedIllGetThatDoneAsSoonAsIShaveThisYak.aspx).
