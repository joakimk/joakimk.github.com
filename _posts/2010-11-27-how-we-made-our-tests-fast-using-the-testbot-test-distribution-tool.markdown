---
layout: post
title: How we made our tests fast using the testbot test distribution tool
---

{{ page.title }}
====

We had a pretty common problem at work. Our test suite was starting to
take way too long to run and the conventional methods like optimizing
slow tests weren't quite enough.

How it used to be:
----

![Slow specs part 1](/images/posts/slow_specs1.png)
![Slow specs part 2](/images/posts/slow_specs2.png)

30 minutes...

How it is now:
----

![Fast specs part 1](/images/posts/fast_specs1.png)
![Fast specs part 2](/images/posts/fast_specs2.png)

**2 minutes!** 

Background:
----

Initially we found the [parallel_tests](https://github.com/grosser/parallel_tests)
project which allowed use of both the cores of our machines which
essentially cut the test runtime in half. As the suite
was still a bit too heavy we started to look for tools that
would allow us to use all our computers when running the tests.

The first project we found was [DeepTest](http://deep-test.rubyforge.org/)
but never actually got up and running with it as it seemed way too complex.

We also found [specjour](https://github.com/sandro/specjour) but it
was designed to work with bonjour which would have limited us to only be
able to run our tests fast when at the office. It
would also not be possible to use other idle server resources.

Testbot
----

So I decided to create my own project, [testbot](https://github.com/joakimk/testbot).
It's been almost a year in the making and this week it was released
[as a gem](https://rubygems.org/gems/testbot)
with a simple command line interface.

Testbot is designed around the idea that test distribution should be
easy. One of the main benefits of the system is that you can add and
remove worker processes at any time and that the user only needs to be
able to access a single server. Because of this you can use spare
resources almost anywhere, developer workstations, virtualized servers,
[PXE booted clusters](https://gist.github.com/622495), [Amazon EC2](https://github.com/joakimk/cloud_bot), ...

A short example of how testbot is used:
- On a computer, e.g. **192.168.0.10** you run **testbot --server**
- On multiple computers you run **testbot --runner --connect 192.168.0.10**
- On your computer you run **rake testbot:spec**
- Done!

Testbot currently supports Test::Unit, RSpec, Cucumber, Rails 2 and Rails 3. 

More info at [http://github.com/joakimk/testbot](http://github.com/joakimk/testbot)

Try it out!

     gem install testbot


