---
date: 2016-01-14 22:25:11+00:00
title: nginx & letsencrypt.sh
slug: nginx-letsencrypt-sh
tags: [deployment, letsencrypt]
---

I've started using [Let's Encrypt](https://letsencrypt.org/) a
[lot](https://www.dominicrodger.com/2016/01/01/moving-away-from-cloudflare/),
for all my domains in fact. Previously, I've been using
[letsencrypt-auto](http://letsencrypt.readthedocs.org/en/latest/using.html#letsencrypt-auto),
and stopping my webserver every time I want to renew a
certificate. This is probably fine (all the sites and domains I run
are low traffic, and can afford to be down for 30s or so when
certificates need renewing every few months), except a flaw in my
process for renewing certificates meant I took my webserver down for
12 hours or so. Twice.

<!-- more -->

After reading [a
post](https://www.metachris.com/2015/12/comparison-of-10-acme-lets-encrypt-clients/)
which described various alternatives to letsencrypt-auto, I thought
[letsencrypt.sh](https://github.com/lukas2511/letsencrypt.sh) looked
fairly good. It's a bash script which takes a list of domains, and
just talks to the Let's Encrypt ACME server for me, by putting some
files somewhere which my webserver could serve.

Installation is simple, if a little jarring[^1]:

```
git clone https://github.com/lukas2511/letsencrypt.sh
```

The next step was just ensuring that nginx would serve the ACME
responses correctly.

All my sites have a configuration a little like this, to ensure all
HTTP requests are redirected to HTTPS:

```
server {
  listen 80;
  server_name dominicrodger.com www.dominicrodger.com;

  location / {
    return 301 https://www.dominicrodger.com$request_uri;
  }
}
```

It's this block - rather than the HTTPS block below, where I set up
either WordPress or Django - which I need to modify, since the ACME
client is going to talk over HTTP[^2].

All I needed to do (though figuring this out took me an
embarrassingly long time) was add this, somewhere in the same server
block:

```
location ^~ /.well-known/acme-challenge/ {
  default_type "text/plain";
  root /var/www/letsencrypt;
}
```

Then, I just needed to create
`/var/www/letsencrypt/.well-known/acme-challenge`[^3]. Once I'd done
that, I could create a `domains.txt` file which just looks like this:

```
dominicrodger.com www.dominicrodger.com
```

Then, all that's needed to get my certificates generated is to run:

```
sudo /etc/letsencrypt.sh/letsencrypt.sh --cron
```

That'll stick certificates in `/etc/letsencrypt.sh/certs` (this assumes that that's where you've put the letsencrypt.sh bash script, config.sh file, and domains.txt, and you've broadly stuck with the defaults in the provided [config.sh](https://github.com/lukas2511/letsencrypt.sh/blob/master/config.sh.example)).

As you'd expect, you can set that up to run regularly with crontab, and you'll always have fresh SSL certificates for free.
  *[ACME]: Automated Certificate Management Environment

[^1]: I'd prefer not to install it with git, but it works.

[^2]: It can't talk over HTTPS, otherwise we'd have a bootstrapping
      problem, since we're presumably using ACME to provision an SSL
      certificate, when one may or may not exist.

[^3]: This confused me for a really long time - I'd expect that the
      location block above would mean that a request to
      `/.well-known/acme-challenge/foobar.html` would look for
      `/var/www/letsencrypt/foobar.html`, but that turns out to be
      wrong - it looks for
      `/var/www/letsencrypt/.well-known/acme-challenge/foobar.html`.
