---
layout: post
title: "WeiBlockMe - 基于关键字的新浪微博屏蔽插件"
date: 2011-10-04 15:05
comments: true
categories: [微博, sina, chrome, extension]
tags: [微博, sina, chrome, extension]
---
国庆节了，大家都跑去外面玩。悲催的我在节前某天晚上加班回家把车给挂了，送去修需要好几天。毕竟别人修车厂也要过国庆呐。宅在家里无所事事的看着微博。一条有一条的重复信息看得人心烦意乱。好吧，不如趁这个机会把我前几天想到的Chrome的基于关键字的微博屏蔽插件做了吧。

说做就做，很快基本的原型就好了。

这是options页面，可以查看你的屏蔽关键字，删除关键字，添加新的关键字：

{% img /images/weiblockme/screen-options.jpg %}

在微博的页面上，选中想要屏蔽的文字，然后点击右键菜单中的“Block Weibo with keyword: xxxx”，即可把选中的文字加入到屏蔽列表中：

{% img /images/weiblockme/context-menu.jpg %}

注册了Chrome Developer，把这个小扩展打包发布了，但是目前还是pending review状态。等通过了review就可以share给大家使用了：

{% img /images/weiblockme/publish-status.jpg %}

从编码到发布的时间很短，后面计划加入下列改进/功能：

  * 增加FireFox，Safari的插件实现
  * 重复的屏蔽关键字检测
  * 根据微博里面带的图片的url进行屏蔽检测
  * 更直观的屏蔽关键字选择，不使用右键菜单，而是直接在选中的文字上添加overlay
  * options页面用tag cloud的方式显示根据关键字屏蔽的微博的数量
  * 查看被屏蔽的微博

想要做的总是很多，看看有没有时间吧：）