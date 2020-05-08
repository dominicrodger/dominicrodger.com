---
date: 2016-01-01 15:58:26+00:00
title: Moving away from Cloudflare
tags: [deployment]
---

I was a very happy user of [Cloudflare](https://www.cloudflare.com/)
for a year or so, primarily after hearing about [Universal
SSL](https://blog.cloudflare.com/introducing-universal-ssl/),
following a failed attempt to get an SSL setup which [SSL
Labs](https://www.ssllabs.com/ssltest/) approved of. I felt uneasy
about it - it seemed like I was intentionally MITM'ing all my sites
by passing them through Cloudflare's network.

<!-- more -->


Whilst I trust Cloudflare[^1], I didn't like the idea that they could
(if they wanted) take a look at every authentication attempt on my
site.

A few weeks ago, [Let's Encrypt](https://letsencrypt.org/) arrived,
and the main motivation behind my use of Cloudflare disappeared,
since it's now fairly trivial to get a trusted SSL certificate for
free, and renewed automatically (I'll post a bit about how I've got
this set up over the next week or two), and thanks to [Mozilla's
handy SSL configuration
generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/),
I no longer need to think too much about what cipher suites and SSL
protocols to support - people who know more about this than me have
made recommendations I can use.

The main driver behind Cloudflare (at least as far as I can see) is
performance and threat protection, but given I run a handful of sites
for churches, charities and friends, I'm not too concerned - I can
get good enough performance for the most part with a bit of caching,
and the sites I run don't seem like particularly reasonable targets
for someone who fancies knocking a site offline.

So, as of a week or two ago, I'm back with my (single) Digital Ocean
droplet exposed to the big bad world. Let's see how I get on.

[^1]: Not for any particularly good reason, other than assuming good
      faith, and the fact that they seem friendly.
