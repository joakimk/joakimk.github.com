---
layout: post
title: Acts as state machine not saving to the database?
tags: aasm rails ruby statemachine
---

{{ page.title }}
====

Just wasted a bit of time figuring out why my tests ran just fine, while trying to enter a state when
actually using the app failed. Turns out that because I do not touch the database in my state machine specs
I had no chance of discovering that I had written "**aasm_event :foo! do**” instead of
"**aasm_event :foo do**" (see that extra "**!**"?)... D’oh. Hope this can be of help
to anyone else that accidentally adds an ! to a aasm_event :).

