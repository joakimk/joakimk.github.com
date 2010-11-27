---
layout: post
title: RSpec controller specs gotcha...
tags: controller rails rspec specs
---

{{ page.title }}
====

Another little tip. If you’re using an IP-based check for access (for example an
before_filter in application.rb), the controller specs will fail (silently) to
do anything for the ‘get’, ‘put’, ‘update’, ... unless you include ‘0.0.0.0’ as a valid ip.

