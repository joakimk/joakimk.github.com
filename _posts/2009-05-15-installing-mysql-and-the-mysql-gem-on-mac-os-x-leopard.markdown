---
layout: post
title: Installing mysql and the mysql gem on Mac OS X Leopard
tags: leopard macosx mysql rails
---

{{ page.title }}
====

I had some trouble getting the mysql gem to work in Leopard on my new macbook and it
turned out that I had installed the 64bit version of mysql and even though the mysql gem
compiles against that, it does not work.

To begin with I got alerted to this by the warning message:

    DEPRECATION WARNING: You’re using the Ruby-based MySQL library that ships with Rails…

The kinds of errors you might see when using the 64 bit version of mysql is:

    LoadError: Failed to lookup Init function /usr/lib/ruby-ee/lib/ruby/gems/1.8/gems/mysql-2.7/lib/mysql.bundle

How to install a version that works:

1. Get [the 32 bit x86 version](http://dev.mysql.com/downloads/mysql/5.0.html#macosx-dmg) and install.
2. Make sure you got it installed propertly (open a terminal and type mysql -v).
3. Run:

&nbsp;

    env ARCHFLAGS="-arch i386" sudo gem install mysql -- --with-mysql-config=/usr/local/mysql/bin/mysql_config
