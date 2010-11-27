---
layout: post
title: Temporaraly disable Kernel.warn (stderr)
tags: ruby stderr
---

{{ page.title }}
====

If you call some code that print warnings to stderr (like Kernel.warn) that can be safetly ignored,
you can use this trick to temporaraly turn off stderr:

    $stderr = File.open('/dev/null', 'w')
    code_that_prints_to_stderr
    $stderr = STDERR

Or you cound wrap it in a block:

    def disable_warnings
      $stderr = File.open('/dev/null', 'w')
      yield
      $stderr = STDERR
    end

    disable_warnings do
      code_that_prints_to_stderr
    end

**Update**: Just learned that if you’re in the context of a rails app you can do:

    silence_warnings do
      code_that_prints_to_stderr
    end

