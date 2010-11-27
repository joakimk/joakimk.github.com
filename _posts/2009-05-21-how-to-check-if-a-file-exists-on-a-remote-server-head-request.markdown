---
layout: post
title: How to check if a file exists on a remote server (HEAD request)
tags: http mp3 rails ruby
---

{{ page.title }}
====

Here is some code to check if a file exists on a remote server by using a HEAD request. It can optionally check
if the content type is what you expected (if you for example want to only allow “audio/mpeg” links).
Note that this particular implementation only works with http urls.

    require 'net/http'
    
    def exists?(file)
      begin
        # Support for both good and bad URI's
        uri = URI.parse(URI.escape(URI.unescape(file)))
        response = nil
         Net::HTTP.start(uri.host, uri.port) {|http|
           response = http.head(uri.path)
         }
         # .. response.content_type == "audio/mpeg"
         response.code == "200"
      rescue
        false
      end
    end

    puts exists?('http://djspike.se/files/albums/6/31/DJ_SPIKE%20-%20Tight%20(demo).mp3')

I also found a plugin with similar goals but that actually downloads the entire target file instead of
only getting the header (not a good idea when dealing with media files):
[http://www.igvita.com/2006/09/07/validating-url-in-ruby-on-rails](http://www.igvita.com/2006/09/07/validating-url-in-ruby-on-rails)

