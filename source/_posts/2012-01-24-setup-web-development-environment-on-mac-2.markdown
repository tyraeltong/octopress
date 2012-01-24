---
layout: post
title: "Mac Web开发环境设置不完全指南 - 常用软件"
date: 2012-01-24 13:20
comments: true
categories: [mac]
tags: [mac]
---
[上一篇](/blog/2012/01/24/setup-web-development-environment-on-mac)我们进行了简单的系统设置，那么接下来应该开始安装软件了。

###Mac AppStore
现在AppStore已经是Mac OSX的内嵌软件了。很多的工具都可以通过AppStore下载安装使用。当然，使用AppStore需要你有一个账户，下面提到的一些软件需要通过AppStore进行安装，所以如果你还没有账号的话，先去申请一个吧。

###Launcher
{% img left http://www.alfredapp.com/images/alfred-logo.png 215 176 %}

用Mac的人都应该很熟悉大名鼎鼎的`spotlight`了。spotlight虽然已经很好用了，但是还是有不少可以改进的地方。现在市面上就有好几个用于替代spotlight的软件，比如说老牌的`Quick Silver`，和QS的作者被挖到Google去做的的`Google QSB`等。我使用了几个替代品，最终定下来坚持使用的是**Alfred**。

Alfred可以直接在AppStore上搜索出来进行安装。这个App是免费的，也可以选择购买收费的Powerpack实现更多的功能。我自己是用免费的就已经足够了。和spotlight相比，Alfred的速度更快。比如说把下载的程序安装到Applications目录下后，spotlight是不能立即找到的，而Alfred几乎是马上就能找到这个新的程序。

使用Alfred你可以自定义搜索。比如说我遇到问题经常会到StackOverflow上去搜，所以我定义了‘so’这个custom search，当输入‘so myquestion’的时候，默认的行为是到StackOverflow上去搜索。有一个叫[AlfredTips](http://alfredtips.com/home/)的网站，专门收集了Alfred使用的各种自定义搜索和主题，可以去淘一下。另外，从备份的角度讲，Alfred的custom searches都是保存在`~/Library/Application Support/Alfred/customsites`下的`customsites.plist`这个文件里的，所以只要把这个文件备份起来（后面会讲我的备份方法），以后换机器，重装系统，都可以很容易的找回你的custom searches设置了。

当然，有了Alfred，就可以想办法把spotlight给禁掉了，要不然它还是会在后台默默的进行indexing的工作，耗费资源。打开Terminal，跑一下下面这个命令就好了：

    sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist

如果要重新启动spotlight，需要使用如下命令：

    sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.metadata.mds.plist

如果像我这样是处女座有强迫症，想要把系统栏最右上角那个放大镜一样的spotlight的图标也去掉的话，需要使用如下命令就好：

    sudo chmod 600 /System/Library/CoreServices/Search.bundle/Contents/MacOS/Search
    killall SystemUIServer

###输入法
这个没啥好讲的，我用拼音输入法，用过不少不同的输入法，还是QQ输入法最好用。有一点是记得在系统设置的"Laguage & Text"里面把对不同的文档使用不同的输入源这个选项给勾上，要不然像我这样在文本编辑器里用中文写blog，在shell下又要用英文输入的切换来切换去会比较麻烦。

{% img /images/mac-setup/ime.jpg %}

###iTerm2
{% img left /images/mac-setup/iterm2-logo.jpg 215 176 %}

系统自带了`Terminal.app`，但是用来用去还是觉得不是特别的方便，所以找一个替代品是必须的。在Mac下，iTerm2几乎是不二的选择。通过[这里](http://www.iterm2.com/#/section/home)可以下载安装。

Bash（和任何使用了GNU readline库的Shell实现）默认提供了Emacs键绑定，比如说Ctrl+A移动到行首，Ctrl+K删掉当前行的所有输入等等。但是在进行按字移动的时候却比较麻烦。比如说我输入了`sudo chmod +x blahblah'，然后我要把光标从行尾移动到chmod，并删除chmod，改为chgrp，需要按Esc+b，Esc+b，Esc+d，才能做到。这是因在Shell下只能使用Esc键作为Emacs键绑定的escape key。这个让我很不习惯，觉得很影响效率，还好可以通过iTerm2的Keys设置来做一些弥补。

在iTerm2下，按`Command+,` 调出设置窗口（顺便提一下在Mac下所有程序的设置窗口都可以这样打开），在"Keys"这个tab下添加三个Global Shortcut Keys，Action的类型都是“Send Escape Sequence”，看下图就明白了：

{% img /images/mac-setup/iterm2-1.jpg %}

这样设置以后，就可以使用Option+b，Option+f，Option+d进行字操作了。

###Skitch
{% img left https://static.skitch.com/images/skitchlogohead.png 247 101%}
Skitch是我用的最多的截屏软件。和Alfred一样，Skitch是免费的软件，需要通过AppStore下载安装。使用的时候很简单，`Command+Shift+5`是选择部分屏幕进行截图，`Command+Shift+6`是进行全屏截图。截图之后会弹出Skitch的主界面，可以添加标注等等。

###Caffeine
{% img left http://lightheadsw.com/resources/images/logos/caffeine.png 247 101%}
很小的一个App，在你的任务栏添加一杯咖啡的图标，鼠标点击该图标进行功能切换，盛满咖啡的时候，会保持你的Mac运行在最高performance的状态，不休眠，所以叫“Don't let your Mac fall asleep”。我在工作的时候一般都会把这个打开。

###Jumpcut
{% img left http://jumpcut.sourceforge.net/jumpcut.png%}
剪贴板历史支持。源代码和安装包在[sourceforge](http://jumpcut.sourceforge.net)上。虽然在AppStore上不少收费的剪贴板增强软件，也有不少强大的功能，但是对于我来说，这个Jumpcut的功能就基本上已经足够了。除了点击系统栏上的剪刀图标在下拉出来的剪贴板历史里选择内容进行粘贴之外，更有效率的方式是通过快捷键完成这一操作。比如说我的设定是`Ctrl+Command+v`。在Jumpcut的preferences的hotkey里面进行设置。

###MenuMeters
{% img left /images/mac-setup/menumeters.jpg %}
作为一个程序员我希望能够很方便的监控我的内存、网络、CPU等系统资源的使用情况，Menumeters就是这样的一个免费软件。从[这里](http://www.ragingmenace.com/software/menumeters/)下载使用。我一般只打开对网络上传下载速度和内存占用情况这两种监控。

###Mou
{% img left http://mouapp.com/Mou_128.png%}
[Markdown](http://daringfireball.net/projects/markdown/)是程序员常用到的工具，而Mou号称“The missing Markdown editor for web developers”，确实是非常的好用。不仅支持完整的Markdown语法与编辑的同时预览Markdown的html格式输出，还能够通过编辑css自定义输出的格式。比如说我自己就基于Github这个样式进行了一些小修改，在需要写html格式的邮件时我会现在Mou里面编辑好，再把html的输出直接拷贝粘贴到邮件里面去。

{% img /images/mac-setup/mou.jpg%}

###Jing
{% img left http://assets.techsmith.com/images/content/mkt-product-jing/free-jing-sun.png %}
Jing是老牌工具厂商[TechSmith](http://assets.techsmith.com/images/content/mkt-product-jing/free-jing-sun.png)的屏幕录像产品，分为Free版本和Pro版本。Free的已经足够日常的使用了。

安装运行后会在屏幕的右上角出现一小块黄色的区域，鼠标移动上去点击会出现菜单选项，可以录制全屏也可以录制部分屏幕。默认输出格式为swf，Pro版本会有更多的选择。

###TunnelBlick

{% img  left http://tunnelblick.googlecode.com/files/tb-icon-96x96.png %}
TunnelBlick是免费且开源的OpenVPN的图形化界面的实现。从[这里](http://code.google.com/p/tunnelblick/)下载安装使用。可以在网上找免费的OpenVPN服务，但是始终不太稳定。我自己是购买了Linode的VPS服务，然后在我的VPS上搭建了一个OpenVPN的server，然后通过TunnelBlick来进行每天的"身体锻炼"。

###Sublime Text 2
{% img left http://d2o0t5hpnwv4c1.cloudfront.net/1038_sublime/sublime.png 200 200 %}

万能（且快速）的文本编辑器，我把它列为开发人员必备工具。基本上我每天有70%左右的时间在和它打交道。从[这里](http://www.sublimetext.com/2)下载安装使用。

Sublime的优点实在是太多了，这里就不一一叙述，网上应该一搜一大堆，比如说[这里](http://net.tutsplus.com/tutorials/tools-and-tips/sublime-text-2-tips-and-tricks/)。在Sublime Text 2出来之前我一直用Emacs或者TextMate，但是自从开始使用ST2后，就再也不用TM了，Emacs偶尔还会用用org-mode，当也不用来写代码了。

###LiveReload
{% img left http://livereload.com/screenshot.png 300 150%}
做Web开发经常会修改下代码，然后刷新下浏览器看看效果。有了LiveReload，就不需要再手工的做这个刷新操作了。从[这里](http://livereload.com/)下载安装使用。除了应用程序自身外，还需要安装浏览器插件。设置好监控哪个文件夹下的文件变动后，在浏览器上点一下LiveReload的插件按钮，即可连接到LiveReload的app，之后只要被监控的文件夹下符合监控条件（可以自定义，比如后缀名）的文件发生了变化，该浏览器就会自动刷新页面。

###DropBox
{% img  left /images/mac-setup/dropbox.png%}
这个算是大名鼎鼎了，不多说，如果没有账号的可以去申请一个免费的账号。我们团队内部经常通过Dropbox分享一些不适合放到source repo里面的东西。后面我也会讲利用Dropbox备份自己的系统设置文件。

###Evernote
{% img left /images/mac-setup/evernote.png%}

这个也是名声在外的，强烈建议通过App Store安装其App，以及相应的浏览器扩展。值得一提的是Evernote还推出了一个叫`Clearly`的Chrome扩展，在改善阅读体验方面也是相当的好用，建议安装。

###Mindnode
{% img left http://www.mindnode.com/style/img/logo.png 100 100%}
画思维导图的工具，简洁，易用。AppStore上有免费的版本。收费的版本提供了更多的功能，不过我基本用不上。

###Xcode
{% img left https://devimages.apple.com.edgekey.net/xcode/images/xcode_icon.png%}
即使你不进行iOS或者Mac App的开发，最好也把Xcode装上。装好它之后很多Unix Tool的编译依赖问题都解决了。

目前版本是4.x，可以通过AppStore免费下载。

###Github for Mac
{% img  left http://mac.github.com/images/promo-screenshot.png 300 100%}
我们的团队采用Git作为源代码管理工具，而[Github for Mac](http://mac.github.com/)是一个Mac下很好用的Git repo的图形化管理工具。

同类型的工具还有[GitX](http://gitx.frim.nl/)、[SourceTree](http://www.sourcetreeapp.com/)等等。其中SourceTree是由Atlassian(出品Jira的公司)推出的Git/Mercurial图形化管理工具，值得一试。

###Sequel Pro
{% img left http://www.sequelpro.com/assets/images/macbookpro-screenshot.png 200 100%}
如果你经常需要和MySql数据库打交道的话，装一个Sequel Pro吧。从软件的外观来看基本可算作同类中最符合Mac风格的一个，相应的功能也很齐全，还有[bundle](http://www.sequelpro.com/bundles/)可以提供增强的功能，比如说选择数据行拷贝成JSON格式等。

###MongoHub
{% img left http://mongohub.todayclose.com/media/img/icon.png 80 80%}
如果你除了Mysql以外，还需要MongoDB的图形化管理工具呢？好的，[MongoHub](http://mongohub.todayclose.com/)就是一个Native的MongoDB的图形化管理工具。有趣的是，MongoHub的[features](http://mongohub.todayclose.com/features)页的内容和Sequel Pro首页上的[highlight](http://www.sequelpro.com/)基本上一模一样：）

Mac下的软件千千万万，但是我在做web开发时常用到的基本上就是这么多。接下来我会接着讲另外一个很大的部分：[Shell](/blog/2012/01/24/setup-web-development-environment-on-mac-3)。
