---
layout: post
title: How I do unit testing in rails apps
tags: ruby code-design unit-testing testing active-record
---


{{ page.title }}
====

The last couple of years I've always started new rails apps with two test suites. One regular rails test suite that loads the entire app, and one unit test suite that only loads the code under test.

A dedicated unit test suite is great because it gives you much faster feedback and let's you have a fast TDD cycle.

### Unit tests

When I talk about unit tests, I mean tests that only test one thing. A unit of code. That may be a method, a class or a small set of classes. The most important thing though is that the test is about the behavior of that unit. It does not test how the code interacts with the filesystem, the network or other such heavy things. As such it's very fast. A unit test that is slower than a few milliseconds is probably doing something odd.

A good reason to keep tests that fast is that you can run them all the time. Not only before commit or in CI.

### The tools

To add support for running non-rails unit tests to your app, look at the [example app](https://github.com/joakimk/fast_unit_tests_example) I've setup on github. You need to add a few simple files and edit your Rakefile a bit. It should only take a few minutes, and won't affect the rest of the app at all.

In the example app you will see that the Rakefile is changed so that some of the raketasks can be run without loading rails. This is useful for other things as well. I've seen it used for everything from benchmarking to installing project specific githooks.

### The simple stuff

The most simple way to unit test code is to make more units. If you have a long method within a rails model, or one that calls out to private methods, you can probably extract that to it's own object.

    class Invoice < ActiveRecord::Base
      validate do |invoice|
        Invoice::Validator.new(invoice).validate
      end

      def ocr
        OcrCalculator.new(invoice).calculate
      end
    end

Go read [7 Patterns to Refactor Fat ActiveRecord Models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/) for more of this.

### Developing a feature from the outside in

I usually start with a rspec feature test using capybara. I tend to write tests on this level to cover the paths though the controller layer. Usually there are only two. Success and failure.

I use this stategy because it covers both controllers and views. If I feel the need to add more logic to the views I usually extract that into presenters which I unit test separately. The controllers tend to remain very thin and that is good because controllers do enough already filling the role of [web adapters](http://blog.mattwynne.net/2012/05/31/hexagonal-rails-objects-values-and-hexagons/) within your application.

One pattern I've found that work really well in rails apps is to split up the code between integration and logic. This was inspired by Gary's [Functional Core, Imperative Shell](https://www.destroyallsoftware.com/screencasts/catalog/functional-core-imperative-shell) idea.

Say we're creating an invoice. In the controller we have the following code:

    def create
      invoice = CreateInvoice.new(params[:invoice]).create

      if invoice.persisted?
        redirect_to ...
      else
        render ...
      end
    end

The CreateInvoice invoice is the imperative shell and as far as possible it should only care about integration. This makes it perfect for integrated tests that use the database and checks for sent emails, etc.

This class could look something like this:

    # app/models/create_invoice.rb
    class CreateInvoice
      def initialize(attributes)
        @attributes = attributes
      end

      def create
        invoice = Build.new(@attributes).build

        if invoice.save
          Notify.invoice_created(invoice)
        end

        invoice
      end
    end

At most this class requires two integrated tests. One to ensure a valid invoice is persisted and the customer notifed, one to ensure we don't notify if it's not.

The interesting part of this is the inner class, CreateInvoice::Build, which would look something like this:

    # app/models/create_invoice/build.rb
    class CreateInvoice::Build
      def initialize(attributes)
        @attributes = attributes
      end

      def build
        build_invoice
        add_invoice_lines
        invoice
      end

      private

      attr_reader :invoice

      def build_invoice
        @invoice = Invoice.new(@attributes)
      end

      def add_invoice_lines
        # Build invoice lines, apply VAT, etc.
      end
    end

This inner class would be tested within the non-rails unit test suite. I'd probably use some fake variant of the Invoice if it's an ActiveRecord model, but it is possible to load those too within a reasonable time if you would like to.

The point here is that the inner class contains all the logic, which is exactly what unit testing is good at. I've found that this pattern works well in most cases where you need to load up some data, make some decisions, build some kind of objects and then persist them.

If you call these classes CreateInvoice, CreatesInvoice, InvoiceCreator, Build, Builder or InvoiceBuilder does not really matter. Use something that fits the style of your project.

### Working with ActiveRecords

If your domain objects inherit from ActiveRecord::Base and you'd still like to use them within the non-rails unit test suite, you can do that by loading only ActiveRecord.

See [this gist](https://gist.github.com/joakimk/8703877) for more information. I'd like to go deeper into how I do this but this blog post is long enough already :).

### What about just using filters?

Why do we need an extra test suite anyways... why not just filter out the tests that don't touch the database and run that as a separate test suite. Well.. I've tried that and even though it works well for a while, rails apps tend to grow. It might be "just" a few seconds now, but as that "unit test" suite starts to take 30 seconds or more to run you just don't run it as often. Tests that are not run often are not providing as much value as they could.

There is also the design aspect: if you have to be more specific about the dependencies in your code because everything isn't available everwhere anymore, you become more aware of the coupling between the objects within the system. I think Corey Haines [put this quite nicely](http://www.youtube.com/watch?feature=player_detailpage&v=bNn6M2vqxHE#t=648) in his "Fast Rails Tests" presentation (at about 10:40):

**Test-First**: Pain => change your tests

(eg. using spork, spring, zeus, etc...)

**Test-Driven**: Pain => change your design

(eg. reducing coupling, separating persistance and logic, etc...)
