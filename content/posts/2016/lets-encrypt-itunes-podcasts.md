---
date: 2016-02-29 06:56:51+00:00
title: Let's Encrypt & iTunes podcasts
slug: lets-encrypt-itunes-podcasts
tags: [letsencrypt]
---

**Update (April 2018):** Apple appear to have fixed this in late
2016 - see [this article from
feed.press](https://feed.press/blog/2016/12/16/apple-silently-adds-support-lets-encrypt-certificates-podcast-feeds/).


* * *

Two podcasts I run have disappeared from the iTunes podcast
store. After a few baffling evenings spent debugging a rather
frustrating "Can't read feed" error, it turns out the problem is
fairly simple.

<!-- more -->

The iTunes Store's support for SSL is a bit disappointing, to say the
least. To get a podcast into the iTunes store, you need to make sure
your SSL set-up is supported by Java 6. That means:


1. No SNI support;
2. No support for more than 1024 bit DH parameters.

Note that neither of the above are required for iTunes itself to add
your podcast manually via the URL - it's just the backend of iTunes
which appears to be seriously limited. The latter is particularly
annoying - reducing the security of my sites just to placate
iTunes. Sadly, downgrading to 1024 bit DH parameters didn't help me
in the slightest. I'd now got a valid Java 6 set-up, but still I
couldn't submit my podcast to the iTunes store.

The advice from Apple when I reported the problem (via
[podcastsupport@apple.com](mailto:podcastsupport@apple.com)), though
more responsive than I'd hoped, came in two parts:


> The use of SSL within an RSS feed URL can cause errors, so please
> remove if possible to successfully submit this feed.


I didn't fancy removing SSL from my site just for the sake of iTunes
(and given the set-up of my site, removing it just from the podcast
would be difficult), so I pushed back a little, and then received
this:


> At this time, SSL certificates from Lets Encrypt are not supported.
>
> Consider an SSL certificate a different organization. Podcasters
> have found the following certificates work well:
>
> [https://www.godaddy.com/ssl/](https://www.godaddy.com/ssl/ssl-certificates.aspx)[ssl-certificates.aspx](https://www.godaddy.com/ssl/ssl-certificates.aspx)
>
> [http://www.symantec.com/ssl-](http://www.symantec.com/ssl-certificates)[certificates](http://www.symantec.com/ssl-certificates)
>
> [https://www.thawte.com/ssl/](https://www.thawte.com/ssl/)
>
> [https://www.globalsign.com/en/](https://www.globalsign.com/en/ssl/)[ssl/](https://www.globalsign.com/en/ssl/)
>
> [http://www.entrust.net](http://www.entrust.net/)
>
> [https://www.geotrust.com/ssl/](https://www.geotrust.com/ssl/)
>
> [http://www.affirmtrust.com](http://www.affirmtrust.com/)
>
> [http://comnodo.com/](http://comnodo.com/)


Here's hoping this situation doesn't last long.


* * *

Discussion on [Hacker News](https://news.ycombinator.com/item?id=11193990).
