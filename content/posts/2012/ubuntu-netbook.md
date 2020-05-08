---
date: 2012-07-09 16:46:00+00:00
title: Moving to Ubuntu for mobile development
slug: ubuntu-netbook
tags: [ubuntu]
---

My web-development office is a fast-moving place.

It averages around 50 miles per hour, and I work on a laptop normally
squished between two people on the train to and from the office.

When I happen to have a spare moment to build websites at home, I
tend to use my desktop, which is running Ubuntu. I do my editing in
emacs, where I've gradually got things set up more or less as I like
them.

<!-- more -->

On my laptop that I've been using on the train, I'm working in
Notepad++, because I've never got around to installing emacs on
Windows.


## Hurrah for tiny laptops


I've recently (re-)acquired an Asus Aspire One. It's a pretty cheap
computer that I bought about 3 years ago. I gradually stopped using
it, as it tended to be too slow to do much with (it came with Windows
Vista, which may not have helped).

It's really quite a lot better as a portable laptop than the rather
heavy Dell I was using - mostly because it's light, it's narrower
(which is good for the fairly small seats on the train), and its
battery lasts for approximately a millennium.

Having got used to (and grown rather fond of) Ubuntu on my desktop, I
thought it was worth a go trying it on my Aspire One. It's taken me
several hours (most of which was spent watching progress bars and a
[tragically inevitable Wimbledon
final](http://www.bbc.co.uk/sport/tennis/18757207)), so I thought I'd
write up how I got it working, partly in case I ever need to do it
again.


## Pain


First, I tried downloading the latest release of Ubuntu (12.04),
dumping it on a USB stick, and booting from that. I quickly got into
Ubuntu, only to get an `io-remap` error, at which point the bottom of
the screen went black, and the top half of the screen tried to
display both halves simultaneously. I went through the installation
process anyway[^1], hoping the problem would go away. It didn't.

Next, I thought I'd try Linux Mint. Same thing, no dice.

Next, I did some reading, and discovered that I'd bumped into a known
issue, which may or may not be fixed in the latest pre-release of
Ubuntu (12.10). I downloaded that. Also no dice.

Next, I read some workarounds for Ubuntu 12.04, so I re-installed and
tried those. Still no dice.

I tried Linux Mint again, tweaking some display options. Still no
luck.

Then, I tried Ubuntu 12.04 again, and discovered that if I hit quit
when asked whether I wanted to install Ubuntu 12.04, the screen would
redraw, and everything looked fine. I installed, went to restart, and
was presented with a pitch black screen.

I discovered then that Ctrl-Alt-F1 got me a terminal[^2], but that
wasn't going to help me build websites. I installed some things just
for fun.

Googling around brought me to [this
answer](http://askubuntu.com/a/130847), which told me to type the
magic incantation at the prompt:

```
sudo service lightdm restart
```

A [blog
post](http://blog.bodhizazen.net/linux/ubuntu-12-04-gma500-poulsbo-boot-options/)
linked from that answer told me to edit `/etc/default/grub` and add
this line (in place of the previous setting of
`GRUB_CMDLINE_LINUX_DEFAULT`):

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet console=tty1 acpi_backlight=vendor acpi_osi=Linux acer_wmi.blacklist=yes mem=1920mb"
```

Once I'd done that, it all worked.

Not for the first time, I had a feeling that I didn't really know
what I was doing (though I am of course very happy to be using my
vendor's `acpi_backlight`, I hope he doesn't mind).

Admittedly my laptop's a little old (I think it came out three years
ago or so), but that was a serious amount of pain just to get things
working. Now I've got things working, it's great, but I can't help
wondering how many people would have been sufficiently stubborn to
get this far. I had a similarly painful experience upgrading my
Desktop from Ubuntu 11 to Ubuntu 12, so maybe I've just got really,
really bad luck.


## Good Things


All this means that the editing environment I use on my laptop is now
identical to the one I use on my desktop. I've synced my emacs
configurations with Dropbox, and logged in to Google Chrome so my
bookmarks are synced. I now get to work in a development environment
where I can use
[virtualenvwrapper](https://bitbucket.org/dhellmann/virtualenvwrapper)
and where most things are pip-installable. Even better, I didn't have
to spend a load of money on a Macbook Air, and I get to retain my
free-software smugness.

[^1]: The top half of the screen would redraw to display whatever
      your mouse was on, which made progress a challenge, but not
      impossible.

[^2]: A couple of people seemed surprised that I didn't know that - I
      guess it's the sort of thing you learn once and then just
      assume.
