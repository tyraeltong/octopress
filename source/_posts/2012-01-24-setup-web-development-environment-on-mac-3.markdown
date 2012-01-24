---
layout: post
title: "Mac Web开发环境设置不完全指南 - Shell"
date: 2012-01-24 16:21
comments: true
categories: [mac]
tags: [mac]
---
[上一篇](/blog/2012/01/24/setup-web-development-environment-on-mac-2)介绍的都是GUI的工具，这一篇我会接着讲有关Shell方面的一些经验和技巧。就我所见，对很多从Windows背景转过来的Web开发人员来说Shell是需要迈过的第一道门槛。图形化的操作在很多情况下是很低效的，Shell能够玩得很熟练，将会对工作效率带来很大的提高。

###Homebrew
在debian系的Linux系统（比如Ubuntu）下，可以使用apt-get方便的进行软件的安装，在Mac下也有几种类似的工具，比如说`fink`，`MacPorts`。但是我现在一般只用Homebrew。有关这三种工具的比较，可以参考[这里](http://terrychay.com/article/macports-vs-homebrew.shtml)，[这里](http://www.engineyard.com/blog/2010/homebrew-os-xs-missing-package-manager/)，[还有这里](http://www.quora.com/Should-I-use-Fink-MacPorts-or-Homebrew)。

安装Homebrew只需要在Shell下执行：

    /usr/bin/ruby -e "$(curl -fsSL https://raw.github.com/gist/323731)"

之后需要安装什么东西，可以先用`brew info`查看一下是否可以通过homebrew安装，比如说：

{%img /images/mac-setup/brew-1.jpg %}

如果支持，运行`brew install xxx`即可。下面介绍的大部分Shell下使用的工具都可以通过homebrew安装。

###wget and curl
wget和curl是UNIX tools中很强大的两个，可惜Mac OSX没有默认安装。通过homebrew可以很容易的把这两个工具安装好：

    brew install wget
    brew install curl

###zsh
Bash应该算是Unix类系统下装机量最多的Shell。而[zsh](http://www.zsh.org/)的功能毫无疑问的比Bash强大了很多。可以参考我之前写的一篇简介[The Reason I Choose ZSH](/blog/2011/12/05/the-reason-i-choose-zsh/)。

有很多种方式可以安装zsh，我是使用homebrew了：

    brew install zsh
安装好之后可以把zsh设置为默认的Shell：

    sudo chsh -s /bin/zsh

###oh-my-zsh
如果你直接使用默认安装好的zsh，可能会觉得还不如bash好用。是的，不过这不怪zsh，而是你还没有做好zsh的配置。当然，zsh有非常多的配置，而[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)则提供了很多默认的配置，可以让你很快就体会到zsh的强大之处。

    curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh

或者使用wget：

    wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh

安装好oh-my-zsh之后，可以对`~/.zshrc`进行修改。我做的主要修改是选择了一个我自己比较喜欢的配色主题，以及启用了一对的zsh plugins，并导入了我在`.bash_profile`做所作的所有设置内容。

###tig
上一篇介绍了GitX, Github for Mac, SourceTree等图形化Git管理工具，而tig是一个命令行下的很强大的git管理工具。之前我也写过一篇简短的[介绍](/blog/2011/10/10/tig-a-console-front-end-for-git/)。同样是通过homebrew安装：

    brew install tig

###htop
[htop](http://htop.sourceforge.net/)是一个很强大的交互式processor viewer，可以查看CPU的使用情况，按照各种规则进行排序，过滤等，对在开发的过程中查找performance issue很有帮助。

{%img http://htop.sourceforge.net/htop-1.0-screenshot.png %}

htop也可以通过homebrew安装：

    brew install htop

###autojump
[autojump](https://github.com/joelthelion/autojump/wiki)可真的算得上一个提高效率的神器。平常情况下我们在目录间跳转会使用很多的`cd`命令，但是实际上我们经常使用的路径是一个很有限的集合。autojump提供了一种更快速的跳转到常用路径的方式。首先通过homebrew安装：

    brew install autojump

安装好之后，你还需要按照之前的方式使用cd一段时间，autojump会默默的收集你在各个路径停留以及切换的频率，然后得到一个权重表。这个权重表可以通过`jumpstat`命令查看。比如：

{%img /images/mac-setup/autojump.jpg %}

那么你直接输入`j autojump`，甚至更短些：`j au`，就会跳转到`/home/joel/workspace/autojump`目录下。

###RVM
[RVM](http://beginrescueend.com/)应该是Ruby/Rails开发人员的必备工具。可以方便的安装和管理多个Ruby的发行版本和Gemset。使用curl安装：

    bash -s stable < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)

RVM会自动修改.bash_profile文件，把RVM相关的scripts路径加到系统路径中。使用zsh的话，需要在.zshrc里加入（如果.zshrc里面有引入~/.bash_profile的话就不用了）：

    [[ -s "~/.rvm/scripts/rvm" ]] && source "~/.rvm/scripts/rvm" # This loads RVM into a shell session.

如果加了这一句Shell还是抱怨找不到rvm命令的话，把上面的~换成你的home dir的全路径应该能解决问题。

使用`rvm install`可以安装新的ruby版本，比如：

    rvm install 1.9.3

在安装之前最好看看`rvm pkg`，网站上有更完整的[指引](http://beginrescueend.com/packages/)。如果你的ruby安装好后出现ssl，readline之类的问题的话，通过`rvm pkg`可以解决。

使用rvm除了管理ruby版本以外，一个很重要的概念是[Gemset](http://beginrescueend.com/gemsets/)。Gemset提供了隔离的gem安装环境，同一个ruby版本可以对应n个Gemset，这样可以做到不同的application开发有不同的Gem依赖，做到互不干扰。

一个比较有用的技巧是使用`.rvmrc`文件自动切换ruby版本和Gemset，可以参考[这里](http://beginrescueend.com/workflow/rvmrc/)。

###git-flow
一个我们正在使用的git分支与发布模型，可以参考我之前的[介绍](/blog/2011/12/05/recommend-a-git-branching-model)。

    brew install git-flow

###git-extras
提供了一些额外的git命令，比如说git-summary可以统计所有的contributor的代码提交次数等。

    brew install git-extras

###tree
在Shell下以树的方式查看目录结构的工具。

    brew install tree

###autotest
Ruby/Rails开发的辅助工具，和LiveReload比较类似，监控文件系统，在文件发生变化的时候自动执行测试代码。需要安装到对应的Gemset里面，也可以直接安装到某个Ruby版本的default Gemset里面：

    gem install ZenTest

autotest也有对应的Growl插件，可以在测试完成时提供桌面冒泡提示，需要安装Growl(通过App Store，现在是收费软件)，和autotest-growl gem:

    gem install autotest-growl

我在Rails3.1的application下运行autotest遇到了和`turn`这个gem包相关的问题，如果你也遇到了，解决办法是安装较低的`turn`版本（< 0.8.3）。

目前想到的和Shell相关我常用的工具就这些，[下一篇](/blog/2012/01/24/setup-web-development-environment-on-mac-4)会讲有关配置的一些经验总结。
