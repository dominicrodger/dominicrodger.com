---
date: 2016-01-22 21:12:40+00:00
title: Managing nginx configuration files
slug: managing-nginx-configuration-files
tags: [digitalocean, deployment]
---

I run [quite a few websites](https://www.dominicrodger.com/about/)
now, and I decided it was probably time I stopped editing nginx
configuration files on the server, reloading nginx, and seeing what
happened. I came across [a post from Tyler
Gaw](http://tylergaw.com/articles/how-i-manage-nginx-config), which
explained a setup fairly close to what I wanted. My setup's a little
different, so I thought I'd write about how I got it working.

<!-- more -->

First off, I put this line in the `http` block of
`/etc/nginx/nginx.conf`:

```
include /home/deploy/nginx/droplet1/*.conf;
```

deploy is the username I use[^1], droplet1 is the hostname, and nginx
is the name of a git repository which contains all my configuration
files. This line allows me to have configuration files for nginx
outside of `/etc/nginx/`[^2].

That git repository basically has this structure:

```
global/
  letsencrypt.sh.conf
  ssl.conf
  wordpress.conf
droplet1/
  dominicrodger.com.conf
  ...
  domains.txt
droplet2/
  ...
  domains.txt
```

The configuration files for each server I have (I currently have two)
live in droplet1 and droplet2, and things that are common to them
both live in global (this includes ssl.conf, which sets up SSL
ciphers; letsencrypt.sh.conf, which sets up [a location for using
letsencrypt.sh](https://www.dominicrodger.com/2016/01/14/nginx-letsencrypt-sh/);
and wordpress.conf, which sets up a basic WordPress
installation). Each droplet has a `domains.txt` file, which is
essentially a driver file for `letsencrypt.sh`, which tells it what
domains to generate certificates for:

I have this repository checked out locally, and I make changes to it
and push it up to Bitbucket. Once it's pushed, I have a Fabric
command which essentially does this:


1. Get the sha1 hash of the current git commit, on the assumption
   that we're already in a good state;
2. Update with `git pull`;
3. Run `sudo service nginx reload`, and check the result. If the
   reload failed, then we roll back to the commit from step 1, show
   the last few lines of the nginx error log, and quit;
4. Update any certificates that need updating.
5. Reload nginx again (to pick up any new certificates).

I use a [Fabric](http://www.fabfile.org/) command for all this, which
looks a bit like this:

```
@task
def update_nginx_config():
    with cd('~/nginx'):
        good_commit = run(
            "sha1hash=$(git log --pretty=format:'%h' -n 1); echo $sha1hash",
            quiet=True
        )

        run("git pull")

        result = sudo("service nginx reload")

        if 'fail' in result:
            print red("Resetting to good commit %s..." % good_commit)
            run("git reset --hard %s" % good_commit)
            sudo("service nginx reload")
            run("sudo tail /var/log/nginx/error.log")
            abort("Aborting - nginx has been reset to its prior state.")

        print green("Server configuration updated")

        # Copy domain file over for letsencrypt.sh
        hostname = run("hostname", quiet=True)
        run("sudo cp ~/nginx/%s/domains.txt "
            "/etc/letsencrypt.sh/" % hostname)
        run("sudo /etc/letsencrypt.sh/letsencrypt.sh --cron")

        # Need to reload nginx to pick up any renewed/updated
        # certificates.
        sudo("service nginx reload")

        print "Latest commit is:"
        run("git log -1 --pretty=%B")
```

All this allows me to deploy changes to nginx safely, without running
manual commands on my server. Better yet, I can see what changes I've
made over time, recover from mistakes quickly, and use tools like sed
on my configuration files without worrying about breaking everything.

[^1]: This is based on the steps in Bryan Kennedy's excellent post,
      [My First 5 Minutes on a
      Server](http://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers).

[^2]: And avoids all the irritation that is
      `/etc/nginx/sites-{enabled,available}`.
