---
layout: post
title: "Getting Nginx+Passenger Work With RVM Gemset"
date: 2011-10-04 14:14
comments: true
categories: [ruby-on-rails, nginx, rvm, passenger]
tags: [ruby, rails, nginx, rvm, passenger]
---
Several days ago, we met a problem when deploying our Rails app to a server running Nginx. Here’s the solution to get Nginx+Passenger work (happily) with RVM Gemset.

[Nginx](http://nginx.net) + [Passenger](http://www.modrails.com) is a popular choice of hosting Rails application. Nginx is very efficient and Passenger has the ability to automatically spawn Rails server process to serve (more) incoming requests. In the mean time, we use [RVM](http://beginrescueend.com) as the basis for our development and also deployment. For whom are new to it, RVM is a handy tool(set) to install and manage multiple version of Rubies on your box. In Ruby world, we use ‘[Gem](http://rubygems.org)‘ to expand Application’s capability. So here comes the situation that if you’re hosting multiple applications, and if the gem’s version are conflicting with each other, what could you do? Well, that’s the [RVM’s Gemset](http://beginrescueend.com/gemsets/) comes to the resuce.

RVM’s Gemset create a virtual environment which is isolated from other application’s environment so you don’t need to worry about the version conflicting at all! When I first know about this, I feel like, amazing, this is what I’ll rely on for the rest of my (ruby/rails) life! So after that, I created a lot of gemsets on my development machines.

One tip to use gemset is **[.rvmrc](http://beginrescueend.com/workflow/rvmrc/)**. Basically, below is a simplified workflow:
{% codeblock A basic workflow of using RVM lang:sh %}
rvm install 1.9.2
rvm use 1.9.2 --default
rvm gemset create myapp
rvm gemset use myapp --default
{% endcodeblock %}

Typically when you want to use one gemset, you need to key in below command:

{% codeblock use rvm lang:sh %}
rvm use ruby-1.9.2-p290@myapp
{% endcodeblock %}

But it’s cumbersome to do that every time, isn't it? Especially when you’re switching between multiple application development(well, this is a bad example, let’s imagine if you’re developing one application, and want to check other application source code you get from Github and demo it), it became a burden of your daily life. So obviously the creator of RVM also encountered this problem at some time and came up with a good solution: You simply create a .rvmrc and put it into your application’s folder with something like below:

{% codeblock use rvm lang:sh %}
rvm use ruby-1.9.2-p290@myapp
{% endcodeblock %}

That’s it! The first time you cd into a directory contains a .rvmrc, you’ll be prompt to trust this rvmrc or not. Key in ‘Yes’, then afterwards whenever you cd into it, RVM will automatically switch to the Ruby and Gemset you specified in this .rvmrc — nice!

Usually you’ll have a source repository(like svn, git) contains your source code and also used for deployment and you’ll have your .rvmrc check into the repository. So here comes the problem we’ve met:

We have a Ubuntu server with correct setup of Nginx and Passenger. The first time we deployed the source code to that box, we manually installed the Ruby and create the gemset via RVM. And as usual, we did a

{% codeblock install dependent gems lang:sh %}
bundle install
{% endcodeblock %}

to install all the dependent gems specified in our application’s Gemfile. Actually it happily installed everything we need. But when we browse to the url, a Nginx error page displayed. It says there’re gems needed by the application not installed anywhere. How come? We checked every bits and it turned out the gemset is the culprit — Passenger is not recognize the .rvmrc and thus it’s not using the gemset we specified!
After several hits of Google search results, the solution is to put a setup_load_paths.rb into your rails application’s config directory:

{% codeblock setup_load_paths.rb lang:ruby %}
if ENV['MY_RUBY_HOME'] &amp;&amp; ENV['MY_RUBY_HOME'].include?('rvm')
  begin
    rvm_path     = File.dirname(File.dirname(ENV['MY_RUBY_HOME']))
    rvm_lib_path = File.join(rvm_path, 'lib')
    $LOAD_PATH.unshift rvm_lib_path
    require 'rvm'
    RVM.use_from_path! File.dirname(File.dirname(__FILE__))
  rescue LoadError
    # RVM is unavailable at this point.
    raise "RVM ruby lib is currently unavailable."
  end
end

# Select the correct item for which you use below.
# If you're not using bundler, remove it completely.
#
# # If we're using a Bundler 1.0 beta
# ENV['BUNDLE_GEMFILE'] = File.expand_path('../Gemfile', File.dirname(__FILE__))
# require 'bundler/setup'
#
# # Or Bundler 0.9...
# if File.exist?(".bundle/environment.rb")
#   require '.bundle/environment'
# else
#   require 'rubygems'
#   require 'bundler'
#   Bundler.setup
# end</pre>
{% endcodeblock %}

BTW, it’s also mentioned in RVM’s [reference](http://beginrescueend.com/integration/passenger/) which we didn’t noticed before — reminds me of the importance of reading through the documents again…