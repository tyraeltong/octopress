---
layout: post
title: "Mac Web开发环境设置不完全指南 - 系统设置"
date: 2012-01-24 10:27
comments: true
categories: [mac]
tags: [mac]
---
以前陆陆续续的在Blog上有提到一些Mac下Web开发环境的小经验和技巧，正好年后会有三位同事加入Mac User的队伍，为了能让他们少走点弯路，也给自己理一下思路，干脆写了这样一个Mac下Web开发环境设置不完全指南。

  * 我们在用Ruby on Rails进行Web开发，但是基本上大部分的东西也应该适合其他类型的开发工具和框架。
  * 我所用的OS X的版本是10.7也就是Lion，但是除了系统设置之外的其他部分也应该使用与10.6的版本。

##系统设置
Lion的系统默认安装好后，最好进行一些调整，以最大发挥系统的威力。

###Trackpad 设置
这应该是我拿到新系统后进行的第一项设置了。首先打开你的系统设置（System Preferences)，找到`Trackpad`这一项，进行如下设置：

  * 确认`Tap to click`和`Secondary click`是选中状态。第一个让你轻触触控板即可实现鼠标点击的功能而不需要把触控板按下去，有助于延长触控板的寿命，当然，也更省力气:)第二个是两个指头触控实现鼠标右键的功能。当然，你也可以设置比如说单击触控板的右下角或左下角来触发（点击下图中圈起来的那个三角形下拉菜单），我自己是觉得两个指头比较方便一些。
  {% img /images/mac-setup/trackpad-1.jpg %}

  * 去掉`Scroll direction: natural`的选择。选中该选项会让两个指头scroll页面的方向和以前的OS X的习惯相反，即手指向下移动页面向上移动，我是不太习惯这种移动方式，所以把这一项去掉了。

  {% img /images/mac-setup/trackpad-2.jpg %}

  * 还有一个拖动窗口的选项。虽然Lion提供了三指移动的手势，但我还是有点不太习惯。如果不修改设置的话，在拖动窗口的时候就需要把trackpad按下去，非常的不方便，所以可以在系统设置里面找到`Universal Access`这一项，在里面的`Mouse & Trackpad`下，点击`Trackpad Options`，把Dragging设为“without Drag Lock”。

  {% img /images/mac-setup/trackpad-3.jpg %}

这样Trackpad就基本上设置好了。Trackpad是一个很强大的多点触摸设备，而Lion自带的手势还是比较有限的。如果想要有更多的手势，可以通过安装第三方软件实现。目前免费的有：

  * [MagicPrefs](http://magicprefs.com/)
  * [BetterTouchTool](http://blog.boastr.net/?page_id=1722)

有关这两种Trackpad增强软件的使用这里就不介绍了，需要的可以自行google。

###Hot corner 和 Sreen Saver
使用Windows的时候，我习惯使用Win+L在离开电脑的时候锁屏，在Lion下呢？首先找到`Security & Privacy`，把"Require password after sleep or screen saver begins"勾上，如下图。我选择的是5秒以后才需要输入密码，免得有时误操作启动了屏保输入密码很麻烦。

{% img /images/mac-setup/screensaver-1.jpg %}

然后再开启hot corner：找到`Desktop & Screen Saver`，在“Screen Saver”的设置页面下有一个“Hot Corners...”选项，可以设置当鼠标移动到屏幕的哪个角落时触发何种操作。我的设置是鼠标甩到屏幕右上角即触发屏保。

{% img /images/mac-setup/screensaver-2.jpg %}

###Tab Key
最后一个，当系统弹出的对话框上有几个控件（比如说按钮）时，默认设置是不能用Tab在控件之间进行焦点移动的。按照下图把“Full Keyboard Access”设为“All controls”，就比较方便了。

{% img /images/mac-setup/tab-key.jpg %}

Mac的系统设置还有很多可以优化调整的，但是对于我来说，上述的这些已经足够了。[下面](/blog/2012/01/24/setup-web-development-environment-on-mac-2)我会接着介绍常用的工具。
