---
layout: post
title: Localized dates in rails using JQuery
tags: dates javascript jquery localization rails
---

{{ page.title }}
====

A little snipplet for localizing dates clientside with some unobtrusive javascript. Please
note that this does result in different outputs in different browsers. I’ve used a
regexp to remove the time difference (ex. GMT+2000) to give it a cleaner look.

On the server side simply print the date:

    <div class="date"><%= @user.created_at %></div>

Then in your javascript (for example application.js), do something like this:

    jQuery.each($(".date"), function() {        
        var new_date = new Date(Date.parse($(this).text())).toLocaleString();
        new_date = new_date.replace(/((GMT|UTC)[+-]((\d{4})|(\d{2}:\d{2})))|(\(.+\))/gi, '');
        $(this).text(new_date);
    });

I have verified that this works in Firefox, Opera, Safari, Chrome and IE7.

