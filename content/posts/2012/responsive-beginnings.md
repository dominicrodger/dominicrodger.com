---
date: 2012-12-26 20:09:00+00:00
title: Forays into Responsive Design
slug: responsive-beginnings
tags: [django, bootstrap, webdev]
---

A good friend of mine alters cards for _Magic: The Gathering_ for fun
and profit. Previously, he posted them on his [Twitter
feed](http://www.twitter.com/JamesTMS), along with a brief
description, but that doesn't provide a great way of seeing at a
glance the sort of things he does - a place to point people who ask
for examples of his work.

<!-- more -->

Initially, I suggested [Flickr](http://www.flickr.com), but it didn't
do quite what he wanted. Since I had a few hours to spare, I did what
any self-respecting engineer would do, and offered to build
something. I launched the resulting site today:
[griffinalters.com](http://www.griffinalters.com)[^1].

Since it was a fairly simple site, which basically has only one
interesting page, I thought now was as good a time as any to get
started down the [responsive
design](http://alistapart.com/article/responsive-web-design) path.


## Bootstrap First


_NB. You'll probably understand the rest of this article better if
you take a look at [griffinalters.com](http://www.griffinalters.com)
first._

I started off by using the grid that Bootstrap gives you, along with
the extra responsive CSS file (`bootstrap-responsive.css`). The card
on the left-hand side was a `span4`, and the card thumbnails on the
right-hand side were each `span2`s. This worked - sort of. On large
enough screens (basically screens at least 768px wide), I'd get the
"featured" card on the left-hand side, and the list of thumbnails on
the right-hand side. The problem with this was that it was all or
nothing - above 768px it'd look like the full desktop site, below
768px, it'd just be a single column - with a single thumbnail on each
row below the featured image.


## Hey, look! No span[0-9]!


What I really wanted was the featured card on the left, and a set of
thumbnails on the right, which remained unless there was no space for
thumbnails at all, at which point the thumbnails would drop beneath
the featured card. So, I started from scratch - here is the source
for the list of thumbnails[^2]:

This works more or less as intended[^3] - the wider the
viewport, the more thumbnails there'll be on any given row.

To position the primary card, I floated it left, and wrapped the
thumbnails:

```
<div style="width: 400px; float: left">
    <img src="card1-primary.jpg" />
</div>


<div style="margin-left: 420px">

<div class="thumbnail">
        <img src="card1-thumbnail.jpg" />

<div class="thumbnail">
        <img src="card2-thumbnail.jpg" />
    </div>

    ...
</div>
```

This works great for displays bigger than 420px, but the
`margin-left` means that if we can't fit any thumbnails to the right
of the featured card, then we end up having a blank space to the left
of our thumbnails.

To fix this, we scope the `margin-left` rule to "reasonably large"
displays. We give the wrapper around our thumbnails the class
"additional-cards", and then in our CSS file we write:

```
@media (min-width: 420px) {
    .additional-cards {
        margin-left: 420px;
    }
}
```

This means that we only apply the left margin to our list of
thumbnails if our viewport is at least 420px[^4].

This gets us a bit closer - but doesn't work very well for displays
only slightly wider than 420px - we still end up with the margin to
the left of our list of thumbnails, but if there's not space to the
right of our featured image for at least one thumbnail, we're back to
the earlier problem, where the containing element for the list of
thumbnails gets pushed below our featured image, and has a blank
space to the left. This is easily fixed by adjusting our `min-width`
to 768px, or something similar:

```
@media (min-width: 768px) {
    .additional-cards {
        margin-left: 420px;
    }
}
```

## min-height too!


One slight irritation is that once you've got enough thumbnails, as
you scroll down, the featured image scrolls off the page, and the
thumbnails again have a large empty space to the left of them.

One way to fix this is with `position: fixed`, but this causes
problems for browsers which aren't tall enough to display the entire
featured image and its description - since they're not visible in the
browser's viewport at load time, and you can't scroll to see the rest
of it.

For this site, we know that all images are 350px high, so we probably
need a viewport about 600px high to see the image and its associated
description:

```
@media (min-height: 600px) {
    .primary-card {
        position: fixed;
        float: none;
    }
}
```

And that's almost it: there's a tiny bit more to our responsive
layout, but not a great deal - you can take a look at [the
CSS](https://raw.github.com/dominicrodger/squigcards/master/squigcards/static/squigcards/css/base.css). If
you're interested - all the code that powers the site is on
[GitHub](https://github.com/dominicrodger/squigcards).


## Concluding Thoughts


All this was complicated enough to get (hopefully largely) right on a
site that is effectively just a single page site - but hopefully the
things I've learned in the attempt will be useful as I work on more
complex sites too.

If you've spotted things I've done wrong - feel free to send me a
pull request, or ping me on
[Twitter](http://www.twitter.com/dominicrodger).

[^1]: You'll have to excuse the somewhat basic theming of
      [Bootstrap](http://twitter.github.com/bootstrap/) - it uses a
      fairly unchanged version of the [Cyborg
      theme](http://bootswatch.com/cyborg/).

[^2]: In real life, I didn't use inline styles, hopefully you get the
      idea.

[^3]: The "less" is for the fact that I can't find a neat way to
      remove the right margin on the last element in each row. This
      means that the last thumbnail in each row can't get any closer
      than 20px from the right hand side of the containing element. I
      know it's possible to figure this out in JavaScript, but I'm
      not sure it's worth the effort.

[^4]: Yes, I know this isn't a large enough `min-width` - we'll get
      there in a moment.
