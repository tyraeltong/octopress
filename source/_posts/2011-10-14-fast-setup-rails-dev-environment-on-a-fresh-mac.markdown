---
layout: post
title: "Fast Setup Rails Dev Environment on a Fresh Mac"
date: 2011-10-14 21:28
comments: true
categories: [ruby, rails]
tags: [ruby, rails]
weibo: ['@JeanInChina', '@木易小伟', '@SmartHuang']
---
几天前Jean说起Dev Environment setup的问题，正好北美的JT在Yammer上发了一条消息介绍他们Team的“a project that will get your computer ready for development in 4 minutes”，实际上是在Github上share的[Dotfile](https://github.com/mleglise/dotfiles-public)。我说我们几个做Rails开发的都有类似的东西，是的，类似－－因为我要share的不是dotfile（我们每个人都有不同的偏好，所以dotfile各不相同），而是快速安装rails开发环境所需的几个东西：homebrew, git, wget, rvm, zsh, oh-my-zsh, ruby, rails。

使用非常简单：

{% codeblock lang:sh %}
ruby -e "$(curl -fsSL https://raw.github.com/gist/1287091)"
{% endcodeblock %}

使用前记得先装好XCode，这是唯一的要求。另外在这个脚本运行的过程中会问你一些问题，或者需要输入你sudo的密码。

{% img /images/dev-env/screen.jpg %}

如果在安装过程中都回答‘y’的话，那么在脚本跑完后，你的shell会默认使用zsh，ruby 1.9.2 p290版本应该安装完毕，同时新建了一个gemset叫做‘rails31’，rails 3.1 会安装在这个gemset里面。对于我们来说，这基本上就是所需的全部。另外如果想要见到安装后效果的shell，需要重新启动你的shell窗口，或者"source ~/.bash_profile"(如果你使用bash的话)。