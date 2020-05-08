---
date: 2012-04-12 18:18:00+00:00
title: Getting Better at Testing
tags: [testing]
---

I started with unit testing about 4 years ago. I started writing what
were probably integration tests, when I was working on the database
backend of our application. The tests I wrote were designed to make
sure that our process which saved data actually saved data.

<!-- more -->

Saving data involved creating a document, calling save on it, which
would save it in the file system-based database, which would hit the
file system. Testing it was saved correctly meant calling open on a
document, which hit the database, which hit the file system.


## I'm doing it wrong


A bit later, I came across this quote:


> A test is not a unit test if:
>
> * It talks to the database
> * It communicates across the network
> * It touches the file system
> * It can't run at the same time as any of your other unit tests
> * You have to do special things to your environment (such as editing config files) to run it.

— [Michael Feathers: A Set of Unit Testing Rules](http://www.artima.com/weblogs/viewpost.jsp?thread=126923)</blockquote>


I hadn't done all those things, but most of my tests did at least 2
of them[1.Since writing those first tests, I've started working on a
team which works on database technology, so avoiding talking to the
database is pretty tricky, since if you've not talked to the
database, you've not tested your code.].

Regardless of the philosophical point of whether what I had written
were unit tests, there was a more fundamental problem: **they were
slow**.

In order to set up the tests I had to create a database, and
initialise its schema. Then I had to repeatedly open and close the
database, saving documents, and checking they were saved
correctly. At the end of the tests they had to clear up the database.


## Impatience


> We will encourage you to develop the three great virtues of a
> programmer: laziness, impatience, and hubris.
— [Larry Wall, Programming Perl](http://en.wikipedia.org/wiki/Larry_Wall)</blockquote>


Increasingly I was writing unit tests which I didn't bother to run
during development - they became another item on my check-list of
things to do before checking code in. If they failed, the specific
thing I changed which might have broken them might have been a while
ago, making it hard to track down.

So a while ago I tried a strange experiment. I tried to build an
entire website[2. The website was [Ministry
Today](http://ministrytoday.org.uk), if you're curious. The less kind
amongst you might well suggest that it looks like a site which was
built with design as an afterthought.] without opening a browser. I
didn't quite make it (eventually I had to open a browser to check
that it looked reasonable), but I got all the models and views built
by writing tests for them[3. Most of the tests are part of
[django-magazine](https://github.com/dominicrodger/django-magazine),
and you can see them on
[GitHub](https://github.com/dominicrodger/django-magazine/tree/master/magazine/tests).].

Using a [quick
trick](http://www.dominicrodger.com/tdd-django-south.html), my app's
test suite runs in 2.1s on my machine, which meant that it was pretty
easy to run tests before each commit[4. I really ought to play around
with [pre-commit hooks](http://tech.yipit.com/2011/11/16/183772396/)
for this stuff some day.]. Being able to specify [particular
tests](https://docs.djangoproject.com/en/dev/topics/testing/#running-tests)
to run makes test-driven development a practical possibility.

And each time I start a project this way, with a basic set of tests,
I find that I'm more likely to write tests next time I work on
it. The tests I write help me be confident that any changes I make
are good, and confidence helps me get stuff done, and getting stuff
done makes me happy.
