---
layout: post
title: Testing filter_parameter_logging using RSpec
tags: controller filter_parameter_logging logging rails rspec ruby security
---

{{ page.title }}
====

One way of speccing parameter filtering.

Controller:

    filter_parameter_logging :password

Spec:

    it "should filter password" do
      before_filter, after_filter = {}, {}
      before_filter['user'] = { 'username'=>'foo', 'password'=>'bar' }
      after_filter['user'] = { 'username'=>'foo', 'password'=>'[FILTERED]' }      
      controller.filter_parameters(before_filter).should == after_filter
    end

