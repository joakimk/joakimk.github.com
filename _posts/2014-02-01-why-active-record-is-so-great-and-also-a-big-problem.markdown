---
layout: post
title: Why ActiveRecord is so great and also a big problem
tags: ruby code-design unit-testing testing active-record
---

{{ page.title }}
====

ActiveRecord is great and if you're working with ruby you probably know it pretty well. It's very easy to use and allows you to build apps quickly as long as you avoid creating monorails or god objects.

Even with all that, letting your models inherit from ActiveRecord::Base is a bit of a problem from a design perspective. One way of looking at it is that you're building your app inside of the database adapter (see [Hexagonal Rails](http://blog.mattwynne.net/2012/05/31/hexagonal-rails-objects-values-and-hexagons/)). This means there isn't really a way to develop the domain objects and most everything that uses the domain objects, which is usually almost everything, without having to load that huge dependency which is ActiveRecord.

### Now is that really so bad?

For a little comparison, if you write a plain unit test in ruby you can get feedback in maybe 200ms (0.2 seconds). When you have to load ActiveRecord this becomes something like 1600ms. In most apps this is even worse because you load all of rails.

Yes you can use a pre-loader like spork but that is essentially caching and occasionally it bites you.

I like comparing test runtimes to web page load speeds. When they get over 200-300ms, you don't browse as much. It's the same with tests. You want your unit tests to feel instant so that you use them all the time.

## It's really about design

When a test takes a long time to run it is most likely an indicator of a problem with the design. The only real exception to this is if it's a test that is testing integration.

If you think about it, ActiveRecords are pretty poor at following SRP (single responsibility principe). Some of the things they do are: persistance, validation, form input parsing, query generation and domain logic.

Do you allow the other objects in your system to be like that? :)

## Alterantives?

It seems that at this point there isn't many good alternatives to using ActiveRecord. At [barsoom](barsoom.se) we tried to hide ActiveRecord away behind a datamapper in one of our projects but that hasn't really increased the velocity in any noticable way and has maybe even made development slower.

One big problem with doing that is that stuff just don't work the way you're used to. Another big problem is that there isn't really a datamapper that is as good or better than using ActiveRecord yet. I'm hoping for ROM to get there... someday.

## Working within the system

In a later post I'll talk about how you can make testing a much better experience even if you're stuck with ActiveRecord. For now, if you haven't already, go read [7 Patterns to Refactor Fat ActiveRecord Models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/) and watch [Fast Rails Tests Corey Haines](http://www.youtube.com/watch?v=bNn6M2vqxHE).
