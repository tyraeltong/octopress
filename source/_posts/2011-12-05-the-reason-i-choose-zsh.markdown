---
layout: post
title: "The Reason I Choose ZSH"
date: 2011-12-05 15:52
comments: true
categories: [zsh, shell]
tags: [zsh, shell]
---
[@lanvige](http://weibo.com/lanvige) 今天在微博上show [bash completion](http://lanvige.com/posts/bash_completion/)，我上去回了一个不如试试zsh。@lanvige 希望我说个一二三出来，微博受字数限制，所以放到博客上来了：

* 自动补全

这个应该是一开始使用zsh的初衷吧。zsh的自动补全很强大，我简单的比较了一下bash completion和zsh的补全。区别在于，bash completion只是一个补全的提示，你还得照着提示继续输入。而zsh的提示出来以后，你可以继续用tab在候选的补全里面选择，然后回车选中该补全。

{% img /images/zsh/auto-complete.jpg %}

比如说上面的途中，我输入git，然后tab，就会出来一堆git的命令候选项，然后我继续tab，tab到那一个补全候选项上，当前命令行提示符上的输入就会自动跟着变成该候选项。选中我要的git命令，直接回车，就完成了一次补全操作。对于我这样不记详细命令而又懒得在键盘上多输入几次的人来说，zsh的自动不全显然更适合一些。

再来一个更有意思的。经常看到初学shell的同事在杀进程的时候会先用‘ps’命令找到相应进程的pid，然后再用‘kill’命令去杀。在zsh下，可以直接输入进程的名字用tab补全：

{% img /images/zsh/kill-completion.jpg %}

上图是tab补全后的截屏，而我实际的输入是‘kill rub’+tab。当然，如果有多个候选项可以继续用tab在候选项中进行选择。如果直接‘kill ’+tab，会出来所有进程的列表。

* 输入错误更正

虽然我键盘耍得很熟，但是难免也会有输入错误的情况，比如说我把‘ifconfig’成了‘idconfig’，回车会出来什么呢？

{% img /images/zsh/word-correction.jpg %}

是的，zsh会发现‘idconfig’就是个很扯淡的输入，根本就没有这样一个命令，不过‘ifconfig’倒是个挺不错的选择，所以它给你提示出来了，选择‘y’，就会直接执行‘ifconfig’。

* oh-my-zsh

如果用brew安装了zsh之后不做任何配置，那用起来基本上可能会让你一下就放弃使用的欲望了。所以我在推荐同事使用zsh的时候都会带着推荐一下[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)。这个装好基本上不用再做更多的配置就能实现很多很炫的功能了。而且oh-my-zsh提供了plugin的机制，自带的比如git啊rvm啊这些都很有用。比如说我的shell prompt是这个样子的：

{% img /images/zsh/my-prompt.jpg %}

眼尖的可能一下就能看出来我截屏的时候是在‘src/octopress'这个路径下，同时前面的[]里面的内容显示我在这个路径下rvm切换到了ruby 1.9.2的版本下的octopress这样一个Gemset下。后面的<>里面的内容显示了这个folder在我的git repo里当前所在的branch是’master‘。所有的这些都是通过oh-my-zsh自带的plugin进行了一下简单的prompt配置得到的。

当然，其实zsh的好处一大堆，我可能说个三天三夜都说不完，但是上面简单列的几条，应该基本上足够诱惑不少同学试试看吧？等用上手了之后，再搜一下zsh，你会发现网上其实有一大票玩这个的，各种tips和插件等也很丰富。怎么样，@lanvige 同学？
