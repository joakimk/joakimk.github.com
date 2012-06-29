---
layout: post
title: Experiences designing a unit-testable feature 
tags: ruby unit-testing rails testing
---

{{ page.title }}
====

Thought I'd write down my experiences with trying to design a feature (for a Rails app) in a way so that it's as unit-testable as possible.

The feature is a page to show statistics based on date constraints.

Names below are different from the actual feature.

Call chain: 

    View/Form -> Controller -> Filter
                            -> Stats -> Query
                                     -> ResultList
                                     -> ResultLine
                            -> View
                              -> ResultListDecorator
                                 -> ResultLineDecorator
                              -> Template

Tested using (pure ruby, non-rails) unit tests:

- **Filter**: takes form input, sets filter defaults, etc. Uses Virtus to do this in a concise way.
- **ResultList** and **ResultLine** are domain classes, does some conversion and applies some rules.
- **ResultListDecorator** and **ResultLineDecorator** are used in place of the domain classes in the view, applies some formatting, colors, etc.

Integration tested:

- The entire feature (one acceptance test that uses the feature as a user)
- **Stats**: Connects the query and the domain classes.
- **Query**: Does the actual queries, returns unformatted data.

What I ended up with was 91% unit tests (100 unit tests and 9 integrated tests).

Overall I'm quite happy with it, it's was simple to add new things as I flushed out the feature, the test suite is a dream, etc.

Let's see how it goes over time. There are some people that think that if you don't test everything with integrated tests, then how are you to know that everything works... better not try :P. I think this has just enough integrated tests to make me feel confident and no more.

This post can also be found at: https://gist.github.com/3017504
