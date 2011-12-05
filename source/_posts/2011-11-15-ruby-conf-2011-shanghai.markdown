---
layout: post
title: "Ruby Conf 2011 Shanghai 笔记1"
date: 2011-11-15 14:31
comments: true
categories: ['Ruby', 'Rails']
tags: ['ruby', 'rails']
---
## Rails应用集群在淘宝自动化测试平台的应用


上周去上海参加了RubyConf。得知有这样一个conference是在微博上，当时看了一下日程安排，因为工作的关系，主要是对Rails应用集群在淘宝自动化测试平台上的应用这样一个主题感兴趣。以下是在听来自黄小勇的讲解时做的笔记，应该有不少遗漏的地方，等官方的slides放出来了之后再核对一下吧。

### 如何应对日益增长的需求

手段很简单-拆分。

淘宝的自动化测试平台的功能应该是不断的增长的。从黄小勇的slides上看到的，到后来（应该不是早期了）包含了很多方面的需求。比如测试用例的管理，测试报告的生成，项目管理，缺陷管理等等。按照我自己的经验估计，一开始应该是在一个Rails Application下面做了快速实现，然后发现需求越来越多，代码文件也越来越多，并且随着项目规模的增长，对开发人员（尤其是新加入的开发人员）的要求也越来越高（不是指技术方面，而是说开发人员需要理解非常多的domain相关的概念，才可能通盘理解整个系统），于是开始考虑拆分系统。这一点在今年早9月份在infoq上看到的一篇文章 [Rails系统重构：从单一复杂系统到多个小应用集群](http://www.infoq.com/cn/articles/rails-app-refactoring) 里面提到的

> 除了技术上的问题，复杂的Rails系统对团队建设也会产生不利影响。首先如果某个开发人员提交了测试无法通过的代码，会对其他人的工作产生影响，降低开发效率；其次对于复杂的系统，增加新功能或修复bug都变得比较困难，久而久之程序员就会产生惰性，代码能少改的就少改，严重阻碍了系统的快速进化；最后在团队有新人加入时，会担心由于其不熟悉系统造成系统崩溃，而不敢放手让他真正参与进来，这样对新人的成长是十分不利的。

基本上是一个道理。

### 具体的经验

拆分这个方法大家都不难想到，那么值得借鉴的自然是具体的实施经验了。淘宝的工程师在国内的技术分享上一直都有很不错的口碑，这次黄小勇分享的内容应该都是从淘宝的自动化测试平台具体实作经验中总结出来的，也让我觉得不虚此行。

#### 数据库

从根本上来说，每一个小型的Rails应用的核心，都应该是其管理的数据。既然把应用分离开了，所以数据最好也应该分开。黄小勇采用的是__一个应用一个库__的方式，并且采用Mysql主从数据库做了__读写操作的分离__。我个人甚至觉得，当你的应用已经在管理大量数据，需要做应用拆分的时候，可以从数据这个层面开始考虑，看看什么样的数据之间的关联是最紧密的，再决定把对这些数据进行操作的应用进行分组与分离，以符合高内聚低耦合原则。

#### Master Service

Rails 3.1 引入了 [Mountable Engine](http://railscasts.com/episodes/277-mountable-engines) , 提供了另一种方式的重用（相比Gem）。Master Service自身是一个可以独立运行的Rails Application，但同时也可以做为engine mount 到拆分出来的每一个小型Rails App里面去。Master Service负责提供一些公用的和需要集中管理的功能，比如说配置（config），静态文件（js，images）等的管理。

#### App配置

使用Rails的config [initialization events](http://guides.rubyonrails.org/configuring.html#initialization-events)在进行Application特定的config之前插入通用config。

##### Gemfile的重用

Rails Application依赖于Gemfile来管理dependency。当拆分出来很多个小型Apps的时候，肯定会有不少所有的Apps都会依赖的Gem包，同时可能在version上会有统一的规定。把这些依赖放到各个单独的App的Gemfile里面去维护既容易出错，效率也很低下。但是网上找了一下，Bundler确实没有提供直接重用Gemfile的办法。黄小勇给出了一种解决方法，很简单：直接调用Bundler的__read_file__方法。
假设目录结构如下：

{% codeblock lang:sh %}
  master_service
    - Gemfile
  app1
    - Gemfile
{% endcodeblock %}

那么在master_service的Gemfile里面定义好对通用Gem的依赖关系，然后在app1的Gemfile里面这样写：

{% codeblock lang:ruby %}
  common_gemfile = File.join(File.dirname(__FILE__), "../master_service/Gemfile")
  self.instance_eval(Bundler.read_file(common_gemfile))
{% endcodeblock %}

就可以了。

##### 用户认证管理

* SSO

  没谈到很detail的内容，但是在网上能找到不少的方案，比如说[RubyCAS](http://code.google.com/p/rubycas-server/)。

* Shared Session
  
  通过各种方式（Database，文件，等等）在多个Rails Application之间share session。


##### 批量创建Rails Application

如果需要创建很多个大体结构基本上类似的rails application，重复的使用rails new并指定相同的参数是比较麻烦的事情。实际上rails new提供了-m这个参数，提供了在创建application时使用统一的模版的功能。比如说使用github上的某个rails application template：

{% codeblock lang:sh %}
  rails new APP_NAME -m https://raw.github.com/RailsApps/rails3-application-templates/master/rails3-devise-rspec-cucumber-template.rb -T
{% endcodeblock %}


##### Rails Application间通信
###### 几种不同的方式

* Ruby API的方式
* RESTful的web service
* MessageQueue 
* Webhook + callback

###### Ruby API
* 提供客户端库
* 需要使用的Apps加载客户端库
  * 把Model的读和写接口分开放到readonly和writable两个接口里面
  * remote helpers
  * remote routes
  * Data Access
    * Separate read and write
  
exmaple:

{% codeblock lang:sh %}
  models 
    - readonly
    - writable
  config.to_prepare do
    Bridge.readonly_models(:issue…)
  end

  *acts_as_readonly*
{% endcodeblock %}

* Remote route
  
  通过Bridge直接加载其他app里面定义好的routes。

* Remote helper

  通过Bridge直接加载其他app里面定义好的helpers。

* RESTful web service
  * font-end: Ajax
  * backend: HTTParty/Typhoeus

#### Deployment
* Nginx + Passenger

  这个应该基本上是比较标准的配置了，用Nginx做前端webserver，Passenger做App Server。也有用Nginx+Thin或其他App Server的。

* Host in one domain

  不同的子应用放到子域名下。我的理解是方便使用者访问，但 [@lanvige](http://weibo.com/lanvige) 对此颇有疑问，值得商榷。

* Scoped routes

我的理解是使用scoped routes让routes文件变得更有组织和清晰。比如说：

{% codeblock lang:ruby %}
  scope '/admin' do
    resources :users, :permissions, :projects
  end
{% endcodeblock %}

* syslogger

  通过syslogger把日志写到统一的地方，以供分析监控使用。

* environments

  除了rails标准自带的development/test/production三种环境以外，增加了如下几种（或者更多）：

  * build_daily
  * build_sandbox
  * online_production

#### Others

* Subscribe + Callback

  可以看看 __ActiveSupport::Notifications__

* Background process with delayed_job

  [delayed_job](https://github.com/collectiveidea/delayed_job) 应该是很通用的后台处理gem了。可以使用db，messagequeue等作为后台进程所需数据的中间存储。从Github上看他们正在把存储相关的内容提取出来单独做，这样支持更多的存储方式比如Mongodb会变得更加灵活。

* Exception_notification_rails3

  [exception_notification_rails3](https://github.com/railsware/exception_notification) 在应用发生错误时给配置好的地址发邮件。这个在系统监控方面很有用。

* Rails benchmark
  
  [Newrelic](http://newrelic.com/) 提供了免费的方案（只是基础的监控）[Pricing](http://newrelic.com/pricing)

  [request-log-analyzer](https://github.com/wvanbergen/request-log-analyzer) 这个也是我一直在用的，很强大，可以通过命令行直接输出各种统计结果，也可以导出为html等各种格式。但是不能做实时分析，只能把log文件抓下来以后做。
