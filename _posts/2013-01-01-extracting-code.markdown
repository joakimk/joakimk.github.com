---
layout: post
title: Extracting code
tags: ruby code-design unit-testing testing
---

{{ page.title }}
====

There seems to be a common option that you should not extract code unless you're going to reuse it. I don't think this is nessesaraly a very good reason for not extracting code. When you extract code into small classes you can introduce descriptive names (named domain concepts), only do one thing (single responsibility principle), test in isolation (test driven design, ensure low coupling) and encourage reuse without having to rewrite existing code (composability, open-closed principle).

YAGNI isn't a bad thing to take into consideration, but it seems to be used too often to dismiss good ideas in the name of preceived simplicity of local code over any other quality.
