---
date: 2012-07-05 16:20:00+00:00
title: Avoiding database queries with Haystack
slug: order-n-haystack
tags: [django]
---

## Order(n) is a pretty bad idea.


Originally, my SearchIndexes for Haystack looked a bit like this:

```
class RegularEventIndex(indexes.SearchIndex):
    text = indexes.CharField(document=True, use_template=True)
```

Then, when someone ran a search, and I wanted to display a result,
I'd load up a template (which template I loaded depended on grabbing
the model name, as recommended in [the
docs](http://django-haystack.readthedocs.org/en/v1.2.7/best_practices.html#content-type-specific-templates)),
and any attributes of the model I wanted to display required doing a
database lookup to fetch the data, like this:

```
<h2>{{ result.object.title }}</h2>
{{ result.object.details|linebreaks }}
```

This has a fairly obvious problem - each time you display a search
result, you do a database query.


## Better - avoiding database lookups

I tried avoiding that problem like this:

```
class RegularEventIndex(indexes.SearchIndex):
    text = indexes.CharField(document=True, use_template=True)
    modified = indexes.DateTimeField(model_attr='modified')
    title = indexes.CharField(model_attr='title')
    details = indexes.CharField(model_attr='details', null=True)
```

Which meant writing templates like this[^1]:

```
<h2>{{ result.title|safe }}</h2>
{{ result.details|safe|linebreaks }}
```

The downside of this was that any attributes of the model I wanted to
display had to be hooked up in the `SearchIndex`. This was made worse
by the fact that you had to remember to keep whether or not a
particular field could be null in sync between the model and the
`SearchIndex` (spot the `null=True` in the `details` attribute
above).


## Betterer - pre-render search result templates


At the point when the index is built, templates have access to the
model instance. This doesn't involve any extra queries (we already
had to load the instance to generate the index for it), and is done
once per object change (instead of once per inclusion in search
results), which is probably a win for most sites.

So, I added a field to my `SearchIndex` that looked like this:

```
class RegularEventIndex(indexes.SearchIndex):
    text = indexes.CharField(document=True, use_template=True)
    modified = indexes.DateTimeField(model_attr='modified')
    rendered = indexes.CharField(use_template=True)
```

I then moved my template that was previously used to render search results on the fly, to the location used by Haystack to look for search templates (`search/indexes/kanisa/_rendered.txt`). In that template I have full access to the model (i.e. I can call methods on it, which wasn't possible without a database query before).

My new template looks like this[^2]:

```
<h2>{{ object.title }}</h2>
{{ object.details|linebreaks }}
```

## Best - do all this stuff in a base class

Once I'd added my new `rendered` attribute to my various
SearchIndexes, I realised that all my SearchIndexes were more or less
the same. I no longer needed all the attributes on them which I had
before (since they were only there to avoid database lookups when
generating templates on the fly), so I could now just have a base
`SearchIndex`, which would be fine for most my models[^3].

It looks like this:

```
class KanisaBaseSearchIndex(indexes.SearchIndex):
    text = indexes.CharField(document=True, use_template=True)
    rendered = indexes.CharField(use_template=True)
    title = indexes.CharField(model_attr='title')

    def get_updated_field(self):
        return 'modified'
```

Almost all my models can use this as is, which makes adding new
models to my site search just a case of registering them in
`search_indexes.py` and adding two templates (`_text.txt` for the
searchable content, and `_rendered.txt` for the displayable search
result).

[^1]: This took me a while to figure out, but attributes going in to
      Haystack have already been escaped once, so need to be marked
      as safe.
[^2]: This is the same as the template above, I've just replaced
      result.object with object.
[^3]: A couple of them have custom titles, which is an attribute I
      access directly on search results.
