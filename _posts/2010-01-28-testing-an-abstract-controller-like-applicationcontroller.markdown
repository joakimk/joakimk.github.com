---
layout: post
title: Testing an abstract controller (like ApplicationController)
tags: controller rails rspec testing
---

{{ page.title }}
====

This is one way to test before_filters in your ApplicationController or other abstract controllers.
Make sure you don’t miss the “after” part!

One thing to keep in mind though is that re-loading the routes can take some serious time
(6 seconds in the app Im working on). I’d like to find a better way of testing this.

    require File.dirname(__FILE__) + '/../../spec_helper'
    
    describe ApplicationController do
    
      class TestController < ApplicationController
        def index
        end
      end
    
      controller_name :test
    
      before :each do
        ActionController::Routing::Routes.draw do |map| 
          map.resources :test
        end
      end
    
      after :all do
        load(RAILS_ROOT + "/config/routes.rb")
      end
    
      it "should do something" do
        get :index
        # assert stuff
      end
    
    end

