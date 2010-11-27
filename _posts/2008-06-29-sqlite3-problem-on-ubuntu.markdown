---
layout: post
title: SQLite3 problem on Ubuntu
tags: linux rails ruby sqlite3 ubuntu
---

{{ page.title }}
====

If you install the “sqlite3-ruby” gem and it prints something like this:

    Building native extensions.  This could take a while...
    Successfully installed sqlite3-ruby-1.2.2
    1 gem installed
    Installing ri documentation for sqlite3-ruby-1.2.2...
    Installing RDoc documentation for sqlite3-ruby-1.2.2...

And when you try to use it in your rails application you get an error like this:

    rake aborted!
    no such file to load -- sqlite3/database

    (See full trace by running task with --trace)

You’re probably missing the libsqlite3-ruby package.

    sudo apt-get install libsqlite3-ruby

