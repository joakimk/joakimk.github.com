---
layout: post
title: Installing GitNub
tags: git macosx
---

{{ page.title }}
====

A script to install the [GitNub](http://github.com/Caged/gitnub/wikis) utility (MacOSX Leopard).

**Update:** Had some problems when I reinstalled this version but it seems that they have fixed it in the latest version.

    wget http://s3.amazonaws.com/caged/releases/GitNub_0.9.0.zip
    unzip -o GitNub_0.9.0.zip
    cd GitNub
    sudo cp -r GitNub.app /Applications
    sudo cp nub /usr/local/bin
    cd ..
    rm -rf GitNub GitNub_0.9.0.zip
    sudo /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/gem install open4

Added the special install line for open4 because of issues others had with it (10810 errors)...

To open a repo, just type ‘nub’ in a git repo.

