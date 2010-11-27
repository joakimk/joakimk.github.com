---
layout: post
title: Increasing the maximum build log size for CruiseControl.rb
tags: cruisecontrol integration rails ruby
---

{{ page.title }}
====

I used the following to allow logs larger than 100KB in CruiseControl.rb:

    # Number of times more logging than the default 
    MORE_LOGGING = 5
   
    Build.class_eval do
   
      def contents_for_display(file)
        return '' unless File.file?(file) && File.readable?(file)
        if File.size(file) < 100 * 1024 * MORE_LOGGING
          File.read(file)
        else
          contents = File.read(file, 100 * MORE_LOGGING)
          "#{file} is over #{100 * MORE_LOGGING} kbytes - too big to
           display in the dashboard, output is truncated\n\n\n#{contents}"
        end
      end
   
    end
   
