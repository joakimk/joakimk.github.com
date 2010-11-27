---
layout: post
title: How to disable Globalize's ActiveRecord logging
tags: globalize rails ruby
---

{{ page.title }}
====

Here is how you disable the sometimes excessive logging that Globalize does:

    # Disables Globalize's ActiveRecord logging
    module Globalize
     class DbViewTranslator
       alias_method :orig_fetch_view_translation, :fetch_view_translation   
       def fetch_view_translation(key, language, idx, namespace = nil, original_caller = nil)
         ActiveRecord::Base.silence do
           orig_fetch_view_translation(key, language, idx, namespace, original_caller)
         end
       end
     end
    end

This tip is originally from [http://www.artweb-design.de/2007/9/16/disable-globalize-viewtranslation-sql-logging](http://www.artweb-design.de/2007/9/16/disable-globalize-viewtranslation-sql-logging),
but I had to adapt it a bit to work with my globalize version.

If this does not work with your version of globalize, then just check how it’s
implemented in “lib/globalize/localization/db_view_translator.rb” and adapt the patch.

