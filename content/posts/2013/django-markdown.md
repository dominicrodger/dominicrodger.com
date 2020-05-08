---
date: 2013-01-16 18:48:00+00:00
title: Supporting Markdown in upcoming Django releases
slug: django-markdown
---

[Upcoming releases of
Django](https://docs.djangoproject.com/en/dev/internals/deprecation/#id3)
will remove `django.contrib.markup`, but I still want to use it. I
looked around for alternatives (perhaps a [third party
app](https://github.com/Alir3z4/django-markwhat)?), before deciding
to roll my own.

<!-- more -->

Here's why:


1. It's ridiculously easy.
2. It means I can enforce which extensions are turned on[1. I always
   enable `nl2br`, which makes newlines act like newlines. Generally,
   most users of my sites are non-technical, and find "end a line
   with two spaces for a linebreak" confusing.], and enable safe
   mode, and generally do things a third party library would have to
   make optional.
3. It means I can add quick extensions to Markdown to support
   features that I might need on my site, and do that in one place
   (e.g. allow convenient referencing of usernames).
4. I don't have to care about supporting old versions of Markdown.

Here's the code, which I've stolen from Django 1.4, and then gutted:

```
import markdown
from django import template
from django.template.defaultfilters import stringfilter
from django.utils.encoding import force_unicode
from django.utils.safestring import mark_safe

register = template.Library()

@register.filter(is_safe=True)
@stringfilter
def my_markdown(value):
    extensions = ["nl2br", ]

    return mark_safe(
        markdown.markdown(
            force_unicode(value),
            extensions,
            safe_mode=True,
            enable_attributes=False
        )
    )
```

I just need to stick that in a file in my `templatetags` folder, then
in my template:

```
{% load myapp_markup %}
{{ value|my_markdown }}
```

That's it.
