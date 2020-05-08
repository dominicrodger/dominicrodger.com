---
date: 2016-01-04 19:50:43+00:00
title: Setting up Digital Ocean to run WordPress
slug: digital-ocean-and-wordpress
tags: [wordpress, digitalocean, deployment]
---

I've recently started launching WordPress sites - starting with
[Talitha](https://www.talitha.org.uk), and now this site. In my haste
to move a few sites that seemed like they'd work better as WordPress
sites, I appear to have over-loaded my single Digital Ocean droplet,
so it's time to spin up a new one.

<!-- more -->


As ever with Digital Ocean, they have an incredibly good tutorial on
[setting up WordPress on
Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-12-04)[^1],
but since I like automating things, I thought it was worth recording
the steps I took (partly for my reference). First off, I ran a
slightly modified version of [my Digital Ocean set up
script](https://github.com/dominicrodger/digitalocean)[^2]. This
script does a few things (mostly based on [Bryan Kennedy's excellent
post](http://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers)
on getting a new server configured):


1. Installs all the things (emacs, git, fail2ban etc);
2. Sets up a non-root user, and prevents logging in as root, or
   logging in with passwords;
3. Set up automated security updates.
4. Run through the steps in another [tutorial for securely applying
   WordPress
   updates](https://www.digitalocean.com/community/tutorials/how-to-configure-secure-updates-and-installations-in-wordpress-on-ubuntu).

Once I'd done that, getting WordPress installed just took a few
steps:

```
sudo apt-get install mysql-server php5-mysql
sudo mysql_install_db
sudo /usr/bin/mysql_secure_installation
sudo apt-get install php5-fpm
```

From there, I needed to:

1. SCP the old WordPress site over to my new droplet (both the files
   and the MySQL database);
2. Repoint my DNS records to point to the new droplet;
3. Set up my new SSL certificates from [Let's
   Encrypt](https://letsencrypt.org/) (I've been meaning to write
   about my Let's Encrypt set-up for a while - it's simple, but works
   well for the low traffic sites I look after).

If you're reading this, the process worked!

[^1]: The tutorial mentions 12.04, but the steps work just fine for
      14.04.

[^2]: The modifications were just to remove the normal
      Python-specific things I need for Django sites.
