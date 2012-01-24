---
layout: post
title: "Mac Web开发环境设置不完全指南 - dot files"
date: 2012-01-24 17:34
comments: true
categories: [mac]
tags: [mac]
---
##dot files
dot files是指以`.`开头的文件，比如说`.bash_profile`。在类Unix系统下，dot files是默认隐藏的。在Shell下，很多的工具使用dot file作为默认导入的配置文件。比如说Bash对应的`.bash_profile`和`.bashrc`。在Shell下查看这些文件需要用`ls -a`。在你的home directory下用这个命令，你可能会看到一些已经存在的dot files。

###.bash_profile & .bashrc
虽然现在我只使用zsh，但是在切换到zsh之前，积累了一些bash相关的配置，所以也在这里一并列出来。首先是这两个文件的区别：

    .bash_profile 是以交互式、login方式进入bash时会调用的

    .bashrc 是交互式、non-login暗示进入bash时会调用的

所以我的.bash_profile里面只有一个调用.bashrc文件的代码，所有的其他设置都放到了.bashrc里面：

    if [ -f ~/.bashrc ]; then
      source ~/.bashrc
    fi

.bashrc里面主要是对一些常用的命令添加了缩写的别名，所以以前我和Eric经常会说一些黑话，比如说gps(git push)，gpl(git pull)之类的，而其他人不知道我们在说啥：）

    alias ssh="ssh -X"
    alias md="mkdir -p"
    alias rd="rmdir"
    alias df="df -h"
    alias mv="mv -i"
    alias slink="link -s"
    alias sed="sed -E"
    alias l="ls -l"
    alias la="ls -lhAF"
    alias ll="ls -lhF"
    alias lt="ls -lhtrF"
    alias l.="ls -lhtrdF .*"
    alias cd..="cd .."
    alias cd...="cd ../.."
    alias cd....="cd ../../.."
    alias ..="cd .."
    alias ...="cd ../.."
    alias ....="cd ../../.."
    alias zb="cat /dev/urandom | hexdump -C | grep --color=auto \"ca fe\""
    alias emacs23="open -a Emacs"
    alias gs="git status"
    alias ga='git add'
    alias gpl="git pull"
    alias gps="git push"
    alias gc="git commit -m"
    alias rdm="rake db:migrate"
    alias rds="rake db:seed"
    alias rr="rake routes"

注意`zb`是我找到的一个很酷的装B命令：）

###.gitconfig
这个文件里面存储了和git相关的配置：

    [user]
      name = Tyrael Tong
      email = Tyrael.Tong@activenetwork.com
    [core]
      quotepath = false
      excludesfile = /Users/tyraeltong/.gitignore_global
    [color]
        diff = auto
        branch = auto
        status = auto
    [color "branch"]
        current = yellow reverse
        local = yellow
        remote = green
    [color "diff"]
        meta = yellow bold
        frag = magenta bold
        old = red bold
        new = green bold
    [color "status"]
        added = yellow
        changed = green
        untracked = cyan
    [merge]
        tool = opendiff
    [alias]
        co = checkout
        ci = commit
        st = status
        br = branch
        type = cat-file -t
        dump = cat-file -p
        hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short
        server = daemon --verbose --export-all --base-path=.
    [push]
      default = current
    [difftool "sourcetree"]
      cmd = opendiff \"$LOCAL\" \"$REMOTE\"
      path =
    [mergetool "sourcetree"]
      cmd = /Applications/SourceTree.app/Contents/Resources/opendiff-w.sh \"$LOCAL\" \"$REMOTE\" -ancestor \"$BASE\" -merge \"$MERGED\"
      trustExitCode = true

主要是设置了有颜色的git status/branch/diff输出（我比较喜欢五颜六色的Shell），配置了一些git命令的缩写（其中的`hist`命令很好用），以及配置使用Xcode自带的`filemerge`作为图形化的diff和merge工具。

###.gemrc
和RubyGems相关的配置：

    ---
    :backtrace: false
    :benchmark: false
    :bulk_threshold: 1000
    :sources:
    - http://rubygems.org/
    - http://gems.github.com
    - http://ruby.taobao.org/
    :update_sources: true
    :verbose: true
    gem: --no-ri --no-rdoc

添加了taobao的rubygems镜像源以提高速度，在最后一行设置安装gem包的时候默认不安装相应的文档。

###.pryrc
Pry是用于替代IRB的，可以参看我之前的[简介](/blog/2011/10/14/introducing-pry)。

    require 'hirb'

    Pry.config.print = proc do |output, value|
      Hirb::View.view_or_page_output(value) || Pry::DEFAULT_PRINT.call(output, value)
    end

只是设置默认使用hirb来格式化输出的数据，方便在rails console下查看数据。

###.vimrc
我之前都是使用Emacs，偶尔也会用一下vim（主要是在查看代码时）。当然我自己不会配vim，直接使用的是别人配得很完善的包：[spf13-vim: A better Vim Distribution](http://spf13.com/project/spf13-vim)。源代码在[github](https://github.com/spf13/spf13-vim)上。通过curl安装：

    curl https://raw.github.com/spf13/spf13-vim/master/bootstrap.sh -o - | sh

##备份你的dot files
上面的这些配置是慢慢积累起来的，而且基本上涵盖了我日常使用的各种Shell工具的配置，所以最好是能够把他们都备份起来，这样重装系统或者使用新的机器的时候能够很快的恢复最熟悉的使用环境。谈到备份，有很多种方式，我自己用的有两种：存在`Dropbox`里或者存在`Github`上，同时保存一个shell脚本`bootstrap.sh`：

    #!/bin/bash
    cd "$(dirname "$0")"
    git pull
    function doIt() {
      rsync --exclude ".git/" --exclude ".DS_Store" --exclude "bootstrap.sh" --exclude "README.md" -av . ~
    }
    if [ "$1" == "--force" -o "$1" == "-f" ]; then
      doIt
    else
      read -p "This may overwrite existing files in your home directory. Are you sure? (y/n) " -n 1
      echo
      if [[ $REPLY =~ ^[Yy]$ ]]; then
        doIt
      fi
    fi
    unset doIt
    source ~/.bash_profile

如果是从Dropbox恢复：

    cd ~/Dropbox/dotfiles
    ./bootstrap.sh

如果是从Github恢复（替换成你自己的repo）：

    git clone https://github.com/tyraeltong/dot-files.git
    cd dot-files
    ./bootstrap.sh

##从别人的经验中学习
这里我列出的实际上只是很少的配置，其中大部分内容都是从网上找到后加入的。现在有不少人在使用我上面提到的第二种方式在Github上保持自己的dot files，提供了一个很好的学习的源，可以不时的到Github上搜索一下"dotfiles"，能够得到不少别人总结好的配置。

从最开始的系统配置到此，你的Mac应该基本上能够胜任进行Web开发（Ruby/Rails)的需要了。
