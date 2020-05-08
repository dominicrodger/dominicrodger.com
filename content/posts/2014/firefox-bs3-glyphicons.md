---
date: 2014-01-19 18:09:00+00:00
title: Firefox & Bootstrap 3 Glyphicons
slug: firefox-bs3-glyphicons
tags: [webdev]
---

I'm currently working on a site which uses Bootstrap 3, and makes
significant use of glyphicons. As of Bootstrap 3, glyphicons are back
to being web-font based. They look fine in Chrome (which is my main
desktop browser), and in Safari on iOS, but I recently noticed they
didn't work on my Android phone.

<!-- more -->

Initially, I thought it might be an Android problem, but then I
spotted that they didn't work in desktop Firefox either. Debugging in
desktop Firefox is rather easier than its mobile cousin, and loading
up the web console, I saw this error:


> Error: downloadable font: download failed (`font-family: ...`): bad
> URI or cross-site access not allowed.


Some Googling around got me to [this Stack Overflow
question](http://stackoverflow.com/questions/15024333/downloadable-font-on-firefox-bad-uri-or-cross-site-access-not-allowed),
which told me I needed to add `Access-Control-Allow-Origin *` to the
headers returned in the HTTP responses for the font files. The answer
was for Apache, where I was using nginx, for which the relevant
snippet is:

```
location ~* .(eot|ttf|woff)$ {
  add_header Access–Control–Allow–Origin *;
}
```

I just added that to the `location` block that was responsible for
serving my font files, reloaded nginx, and the icons appear
correctly.

Here's hoping this post helps someone else figure out how to get this
set up.
