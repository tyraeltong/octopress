---
layout: post
title: "Recommend a Git Branching Model"
date: 2011-12-05 17:04
comments: true
categories: [git]
tags: [git]
---
说到source control，因为人上了些年纪的关系，以前用过不少，从最开始的vcs，到cvs，到svn，甚至什么mercurial啊clear case啊之类的都或多或少用过。从最开始的就把它当一个源代码备份工具，到后来了解到一些比较繁杂的源代码管理工作，有一个比较大的感触就是在多人同时开发的环境下，分支与合并始终是一件很麻烦的事情。Git，据说就是Linux之父在和很多人一起开发linux kernel的时候，实在是忍无可忍，无法再忍了用几天的事件开发出来的，而且一开始的目的就是为了让分支和合并的操作尽量的“廉价”。虽然我一直在自己工作之余尝试着使用git，但是直到我们开始做一个内部项目的时候，才开始真正每天都使用到了这个号称非常牛逼哄哄的源代码管理工具。当然，一用之下基本上就再也不想回到公司使用最多的svn上了。网上有关git的好处，与其他源代码管理工具的异同优劣之类的比较文章很多，都不是今天我写这个blog的重点。这个blog的重点是想要推荐一个我觉得比较好的使用git做分支与合并的开发模型。

实际上不管源代码管理工具是什么，你的项目采用的分支与合并的开发模型都很重要。相信需要用到分支与合并的项目基本上都会涉及到不少人，没有规矩，不成方圆，如果没有一个好的开发模型，再牛逼哄哄的工具都会变得臭烘烘不是么。

好吧切入正题，我要推荐的这个模型实际上在这篇文章里面已经讲得很清楚了：[a successful git branching model](http://nvie.com/posts/a-successful-git-branching-model/)。总结起来，大致如下图所示（摘自 @nvie的blog）：

{% img http://nvie.com/img/2009/12/Screen-shot-2009-12-24-at-11.32.03.png %}

总结起来大致有以下几点：

* repo里面一直存在的branch有两个：

#### master
    
  master分支上的任何一个版本都是可以release到production上的版本，并且tag都是打在master上的。

#### develop
    
  develop分支保持开发过程中的代码变更。但是如果代码变更比较大（或者你是一个纯粹主义者）时，应该把develop分支作为一个集成的分支，所有的代码变更应该在下面提到的几种分支上做好，再集成到这个develop分支上，然后做release到master分支，打tag，做发布。

* 在开发过程中使用三种类型的临时分支管理代码变更

#### feature分支

  在做新feature开发时，应该新建feature分支，所有的代码在到达DoD(Definition of Done)之前，都应该commit到相应的feature分支上。并且由于git自身是分布式源代码管理工具，在实际开发中甚至可以形成开发小组使用feature分支进行代码分享而不用提交到中心server上。

  需要注意的是feature分支应该从develop分支出来，并且开发结束是合并回develop分支。

#### release分支

  在做产品发布时创建的分支类型，在这个分支上做一些配置管理的工作，比如说更改软件版本号之类的。

  需要注意的是release分支应该从develop分支出来，并且发布结束需要合并回develop和master分支。

#### hotfix分支

  当发布出去的版本有继续fix的问题时，使用hotfix分支类型进行问题修复。

  需要注意的是hotfix分支应该从master分支出来，并且fix结束需要合并回develop和master分支。

正如 @nvie blog原文所说，这里列的这个model没有什么令人震惊的内容，但是它所提供的“大局观”对于一个项目健康的源代码管理来说是非常有用的。另外 @nvie 还开发了一个按照这个model对git底层操作进行封装以提供简单的高层操作的[git-flow](https://github.com/nvie/gitflow)。安装是非常方便的，可以直接使用homebrew。git-flow提供了几个命令：

> * git flow init
* git flow feature
* git flow release
* git flow hotfix
* git flow support

比如在进行新feature开发的时候，如果新feature的名字是“login”，那么

{% codeblock lang:sh %}
  git flow feature start login
{% endcodeblock %}

将会新建一个名字为login的分支，此后皆可按照日常所用的git命令进行开发。在feature开发结束后，使用：

{% codeblock lang:sh %}
  git flow feature finish login
{% endcodeblock %}

将会按照此model把这个feature分支上的代码变更合并回develop分支。

另外值得一提的是，如果你用的是zsh，那么[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)自带了git-flow的plugin，只需要在你的.zshrc文件的plugin这一行把它enable就好了。比如说我的是：

{% codeblock lang:sh %}
  # Which plugins would you like to load? (plugins can be found in ~/.oh-my-zsh/plugins/*)
  # Example format: plugins=(rails git textmate ruby lighthouse)
  plugins=(git brew autojump gem history-substring-search rails3 osx)
{% endcodeblock %}