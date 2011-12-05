---
layout: post
title: "Introducing Pry"
date: 2011-10-14 09:07
comments: true
categories: [ruby, rails, irb]
tags: [ruby, rails, irb]
weibo: ['@木易小伟', '@SmartHuang']
---
看到[Pry](https://pry.github.com)这个IRB的替代品出来有一段时间了。上个月曾经试着简单的使用了一下，但是由于时间仓促，没有太多的体会到Pry的好处，就直接回到了IRB的环境。今天早上起来看到在[ruby-toolbox](https://www.ruby-toolbox.com) 上Pry三个大大的字放在了首页的推荐栏上，点进去一看，被它的热度趋势吓了一跳：

{% img /images/pry/popularity.jpg %}

可以看到在10月份（现在才过了不到一半），Pry已经开始火起来了。世上没有无缘故的爱，这么火应该是有原因的，那我就花点时间重新来看看Pry到底有些什么好处吧。因为我主要是进行Rails开发，所以下面的实验都是在Rails环境下进行的。

首先是用Pry替代IRB。这个很简单，稍微google一下就有很清楚的描述。关键代码是在config里面加上

{% codeblock lang:ruby %}
silence_warnings do
  begin
    require 'pry'
    IRB = Pry
  rescue LoadError
  end
end
{% endcodeblock %}

但是这样做有一个问题是，一旦提交了代码，那别人的rails console也会变成Pry。做一个rake task，用类似 "rake pry" 之类的来启动应该会更好。

首先测试一下Pry下原有的常用功能有没有变化。我在Rails Console下最常用的主要也就是查看下model的结构，找找数据，写点临时代码之类的。
{% img /images/pry/exp1.jpg %}
嗯，一切正常。用irb查询数据出来的结果的默认格式比较折磨人，一般都会用Hirb来增强，Pry能否和Hirb配合使用呢？ [cldwalker](https://github.com/cldwalker) 说no。但是还好有人贡献了很简单的办法：在.pryrc里面加上如下代码即可：
{% codeblock lang:ruby %}
require 'hirb'

Pry.config.print = proc do |output, value|
  Hirb::View.view_or_page_output(value) || Pry::DEFAULT_PRINT.call(output, value)
end
{% endcodeblock %}
这是enable了Hirb后的效果，和使用irb时没有两样：
{% img /images/pry/exp2.jpg %}
好吧，既然基本的使用都没有啥迁移的代价的话，那就看看Pry专属的功能吧：

* **shell命令支持**

  我在使用IRB的时候时不时的需要调用一些shell命令，比如说cd到某个目录啊，看看有没有生成相应的文件啊之类的，之前我的做法比较土，要嘛就是直接开两个shell，一个跑irb，一个在需要的时候运行shell命令（后来想到可以利用fg，所以现在是直接Ctrl-Z出来，运行shell命令，然后fg把rails console带到前台）。但是当ssh到服务器上的时候就比较麻烦，需要开两个ssh session，就算用ctrl-z和fg，来来回回的切换也始终是个麻烦事儿。嗯，在Pry下这些麻烦就不再有了。直接在**.**后面跟shell命令就可以运行了。

{% img /images/pry/exp3.jpg %}

  是不是很handy呢？下面这个就更handy了：你可以直接在shell命令里面使用Pry session里面的所有Ruby变量：

{% img /images/pry/exp4.jpg %}

  注意红色线上的代码，使用的是和Ruby的字符串interpolating一样的语法。在很多情况下，这个可以节省不少copy paste的操作。
  根据[官方文档](https://github.com/pry/pry/wiki/Shell-Integration)，Pry还提供了一个shell-mode，实现的功能是在输入的时候把当前目录下的文件名作为tab completion的备选。因为只引入了当前路径（非递归）下的文件名，所以有点鸡肋。

* **对象浏览**

这个有点酷，你可以把Ruby里的对象（可以是类，也可以是实例对象）当作文件夹一样的来浏览，使用cd和ls命令。cd进去以后，有几个show命令。我现在觉得比较有用的是show-source这个命令，可以显示某个方法的代码（包括Gem里面的方法）：

{% img /images/pry/exp5.jpg %}

记得有时代码出问题的时候，从trace里面看到是安装的Gem里面的某个方法的问题，想要看看源代码，那叫一个麻烦啊，cd到gem的安装路径下去，然后用vim打开源代码查看，这下可好，不用离开Pry就能初步判断下问题是什么。而且如果你想要查看这个方法所在的文件的所有内容的话，那也是小菜一碟：edit-method命令会用你shell下配好的编辑器来打开该源代码文件。

{% img /images/pry/exp6.jpg %}

* **编辑你的输入**

我经常遇到这样的情况：有一些很短的实验性代码，懒得打开编辑器写，而是随手就写在了irb的session里。但是输入的时候经常会出现小的错误，比如说烧了个“｝”啊之类的，只有从头再输入一遍，经常按下回车键后发现有个小错误以后想把自己的手都砍掉。Pry引入了对你的输入的管理，在你输入错误后，可以修改（amend）之前的某一行输入，这对我这样的人来说真是个天大的好消息：

{% img /images/pry/exp7.jpg %}

虽然这里列的这些只是Pry的很小一部分功能，但已经足够吸引我用它来替代irb了。强烈推荐观看[官方网站](http://pry.github.com/)上的视频，可以看到还可以用Pry做debug这样的事情。另外，[Pry的wiki](https://github.com/pry/pry/wiki)上也有很多有用的信息可供参考。

