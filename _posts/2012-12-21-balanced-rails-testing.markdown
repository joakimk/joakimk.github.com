---
layout: post
title: Balanced rails testing
tags: ruby unit-testing rails testing
---

{{ page.title }}
====

TLDR: Be more productive building rails apps by using each testing tool where it does the most good.

Most of this only applies to fairly large rails apps. Our current rails project has 4668 tests, 16 KLOC, 122 controllers and 56 tables. We generally follow the rails way in this app except where we feel stepping of the rails is more productive (or where we're simply trying out new ideas).

### Fast feedback

I've always had this tendency to want my tests to be fast. Two years ago I released [testbot](https://github.com/joakimk/testbot) as a way of dealing with it by running tests in parallell. Recently I've been much more interested about finding the root cause of slow tests and a better way to work where I can avoid them as much as possible.

What I found is that the biggest reason we have this problem in rails apps is that we use integrated tests to test basic correctness.

I think J. B. Rainsberger nails the problem in [Not Just Slow: Integration Tests are a Vortex of Doom](http://www.jbrains.ca/permalink/not-just-slow-integration-tests-are-a-vortex-of-doom), but here is the short version: When you test a bit of code using integrated tests you can introduce dependencies without you even knowing it. This in turn makes isolated unit testing quite difficult, which motivates you to write even more integrated tests. It's the "Vortex Of DOOM!" :D.

Integrated tests have many valid uses where they don't do that kind of damage to the design. Things like when you want to know if a SQL query returns the correct result or if a client library works with the external service it is supposed to talk to.

This does not mean that using integrated tests for other things are wrong. I just don't think it's a productive way to build large apps. It may be good starting point when learning how to write tests and is probably the most cost effective way to write tests for small apps.

So what is the alternative? **Unit tests**!

More specifically: isolated unit tests.

I tend to call them isolated unit tests to not confuse them with what you sometimes call unit tests in rails which are far from isolated. An isolated unit test only loads the code under test and should only fail if the code under test is broken. An isolated unit test suite can start in a few hundred milliseconds and each test takes no more than about 1 ms. This means you can have thousands of them without it taking more than a few seconds to run them all.

Isolated unit tests are great for exploring code on the lowest level. They are stable, fast and easy to understand. They allow you to work on one thing at a time and they make it really freaking hard to accidentally introduce high coupling in your code (if you're at all sensitive to having too many stubs).

As a bonus I've found it much easier to do TDD when I'm that close to the code.

### How to begin?

To be able to do isolated unit testing you need to be able to run code in isolation from rails. A good start is adding a [second set of tests to your rails app](https://github.com/joakimk/fast_unit_tests_example). Start with a single test. Then each time you find a concept in your system that isn't dependent on anything else, use the isolated test suite. A few easy ones to start out with is formatting (currency, numbers, time), validation and calculations.

When you've run out of easy parts of the code to test you might want to start thinking about separating integrated code (db queries, etc.) from other code in other to test that code in isolation.

When you do this you introduce more code and abstractions, which has a cost but seems to be a worthwhile tradeoff because:

* Each piece of the code is simpler to understand and change.
* The tests become faster and more reliable.
* The feedback from those tests can be gained right away instead of waiting minutes for an integrated test suite to be run.
<br/>
<br/>

### Hexagonal Rails

This is very much related to Matt Wynne's [Hexagonal Rails](http://blog.mattwynne.net/2012/05/31/hexagonal-rails-objects-values-and-hexagons/):

"The fundamental idea behind this style of architecture is to isolate the core domain of your program—the bit that defines its unique and interesting behaviour—from the technical infrastructure that enables it to talk to the outside world. Technical infrastructure means things like databases, file systems, user interfaces, web services and message queues."

"There are apparent costs too. As I’ve moved the Relish codebase towards a more hexagonal style, I’ve noticed that there’s more code appearing. However, each piece of code is simpler to understand, and the pieces are much less coupled together, giving me flexibility for the future."

### Test pyramid

When you do balanced testing you will end up with a test suite that have mostly unit tests, fewer integrated tests and very few acceptance tests. This phenomenon has been named a test pyramid by Mike Cohn and Martin Fowler describes it well in [http://martinfowler.com/bliki/TestPyramid.html](http://martinfowler.com/bliki/TestPyramid.html).

### Related things you might like
<br/>
* "Look at your Rails unit test suite. Now look at mine. Now look at yours again. Mine are sub-second. Yours aren't.": [http://www.confreaks.com/videos/641-gogaruco2011-fast-rails-tests](http://www.confreaks.com/videos/641-gogaruco2011-fast-rails-tests)
* Excellent screencasts on isolated unit testing, TDD, OO-design and more: [https://www.destroyallsoftware.com/](https://www.destroyallsoftware.com/)
* Really good talk on how to use unit tests effectively: [Hashrocket Lunch n' Learn with Sandi Metz - The Design of Tests](http://vimeo.com/48106365)
* I've written about my experiences building a feature in a way where over 90% of the tests are unit tests: [http://rubyblocks.se/2012/06/29/experiences-designing-a-unit-testable-feature/](http://rubyblocks.se/2012/06/29/experiences-designing-a-unit-testable-feature/).
* [Minimapper](https://github.com/joakimk/minimapper): "...is a minimalistic way of separating models from ORMs like ActiveRecord. It enables you to test your models (and code using your models) within a sub-second unit test suite..."
