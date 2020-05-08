---
date: 2012-04-07 08:39:00+00:00
title: Starting with Pelican
tags: [blog-rewrite]
---

In about 1998, I was building a website for a friend. The site was
entirely static. I'd already learned the hard way about the pain of
trying to keep lots of separate static HTML files visually
consistent.

<!-- more -->

To build the site, I used a system (whose name has since faded from
memory) which took as its input some template files, some files
containing the content, and combined them together to build a
website. I'd run a script, and FTP the resulting HTML to a server
somewhere.

Since then, I've tried various different things. I used
[Greymatter](http://en.wikipedia.org/wiki/Greymatter_(software)) for
a while, and then mostly wrote my own - first in PHP, and then more
recently in Django. The only thing that all these blogs had in common
was this: I spent more time building the system than I ever spent
using it.


## Today


14 years after my first static site generator experience, I'm
back. This time I'm using
[Pelican](http://pelican.readthedocs.org/en/latest/), and hoping that
the auto-reload stuff will make writing easier - as I can write in
[Markdown](http://daringfireball.net/projects/markdown/), and see the
results as they will be rendered live[^1].

Thanks to
[pydanny](http://pydanny.com/choosing-a-new-python-based-blog-engine.html)
for the tip about Pelican - it's been pretty straightforward so far.


## Customising Pelican


The only changes I've really made to Pelican are to the CSS ([fixing
up the code
style](https://github.com/dominicrodger/dominicrodger.com/commit/61e81d568087a92e2bb41dc619966075566fb81e),
because I don't much like the default monospace font, and [modifying
the footnote link
style](https://github.com/dominicrodger/dominicrodger.com/commit/54d99084c8b6777cd77c31fe1157718fc1a612b7)). I
did that by just copying the default theme into my directory (which
I've called `plagiarism`), and modifying my call to re-compile my
blog to:

```
pelican src -s pelican.conf.py -t plagiarism
```



## Deploying Pelican


I use
[WebFaction](http://www.webfaction.com/?affiliate=dominicrodger) for
all my sites, and whilst deploying was easy, it wasn't entirely
obvious that I did it the right way. WebFaction has a really nice
model for managing sites ([domains, applications, and
websites](http://docs.webfaction.com/user-guide/websites.html)), but
Pelican didn't really fit into its model. Eventually I settled on
creating a Pelican installation in my root directory, and then used
"Symbolic link to static only app" with the path set to Pelican's
output directory.

I've added a cron job to my WebFaction account which just pulls from
my public GitHub repository, so any changes I push to
[GitHub](https://github.com/dominicrodger/dominicrodger.com) get
deployed to the site without any intervention.

[^1]: Sort of, anyway - I'm using an auto-refresh extension in
      Chrome, and using the [auto-reload
      functionality](http://pelican.notmyidea.org/en/2.8/getting_started.html#autoreload)
      in Pelican, so I see updates every 5 seconds or so.
