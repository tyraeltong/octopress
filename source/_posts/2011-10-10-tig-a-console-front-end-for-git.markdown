---
layout: post
title: "Tig, A console front-end for Git"
date: 2011-10-10 10:13
comments: true
categories: [git, tool, mac]
tags: [git, tool, mac]
weibo: ['@木易小伟', '@SmartHuang']
---
There're some tools out there on Mac as the Git front-end on Mac, like [GitHub for Mac](http://mac.github.com), [GitX](http://gitx.frim.nl/), etc. Those are all good tools, but for guys like me who're sticking to the shell, it's a bit of pain switching back and forth from those GUI front-end and the shell. Today I just ran into a very suitable tool(for me) - [tig](http://jonas.nitro.dk/tig/), a console front-end for Git - which I may use day to day from now on.

Here's a a screen shot:

{% img /images/tig/overview.jpg %}

To install it on Mac, just use [Homebrew](https://github.com/mxcl/homebrew) or [Mac Ports](http://www.macports.org). I personally using the Brew:

{% codeblock lang:sh %}
brew install tig
{% endcodeblock %}

The usage shall be very familiar to who's addict to the shell. Use Up/Down arrow key or j/k to navigate up and down the history, Enter to see the details of a commit with , like this one:

{% img /images/tig/commit-detail.jpg %}

While in the above mode, Up/Down arrow keys will move in the history list(top one) and j/k will move in the details view(bottom one). After done your investigation on that, use 'q' to quit.

Try press 'h', you'll see a list of the short-cut keys, a lot of good stuff!