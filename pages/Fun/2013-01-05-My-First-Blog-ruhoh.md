---
title: 博客生成记
description: 使用ruhoh搭建个人博客
---

目录

- 开始写博客
- 为什么是Ruhoh+github+markdown
- 安装Ruhoh


Keyword: Markdown, Ruhoh, github, How to,

# 开始写博客 #

时间过得飞快，一转眼世界末日就过去了，一年来在[微博](http://weibo.com/u/1716287123)上结交了不少好友，所以一直在努力更新微博，很少有时间写文档。但通过学习别人的博客获益良多，并且越来越觉得有必要用博客的形式来记录下有价值的思考，并且可以“一次表达，无数次阅读”，写成文字更有益于思考。


第一篇就记录一下[如何使用ruhoh搭建个人博客](yujiedu.ruhoh.com/2013-01-05-My-First-Blog-ruhoh)。


首先介绍一下如何基于开源的ruhoh搭建一个个人博客，以及为什么选择[Ruhoh静态博客](http://ruhoh.com/)。

如果你是一名开源爱好者，一定不会错过[github.com](github.com)，如果你还没有开源项目怎么办？那就不妨从学习使用入手，用它来搭建一个个人博客或许是个不错的开始。


# 为什么是Ruhoh+github+markdown #

提起博客系统你最先想到的可能会是Wordpress，但用静态网站生成系统来做个人的博客还是非常值得尝试的，并且可以结合强大的版本控制系统，纯文本，方便备份，离线也可以编辑和预览，还有markdown的易读性，但前提是你要有一颗折腾的起的心:-)

说起Ruhoh就不得不提一下Jekll，Jekyll的作者（也是GitHub的共同创始人）Tom Preston-Werner曾写过一篇博文 [Blogging like a hacker ](http://tom.preston-werner.com/2008/11/17/blogging-like-a-hacker.html)（中文翻译《 [像黑客一样写博客](http://kyle.xlau.org/posts/blogging-like-a-hacker.html) 》 by Kylexlau）。而Ruhoh和[Jekyll-Bootstrap](http://jekyllbootstrap.com/)是同一位作者。他是这么介绍Ruhoh的：

	ruhoh is an exercise in [sharing](http://sivers.org/sharing).

	ruhoh is inspired by Jekyll and designed based on everything I learned from creating [jekyllbootstrap.com](jekyllbootstrap.com)

	ruhoh is the next iteration of what I think static, technical blogging can be. 

Ruhoh安装部署非常容易，在这之前你也可以先看看Jekyll之类的博客生成系统的工作方式：


# 安装Ruhoh #

1. 安装配置github for windows

GitHub 使用 git 分布式版本控制系统，而 git 最初是 Linus Torvalds 为帮助 Linux 开发而创造的，它针对的是 Linux 平台，因此 git 和 Windows 从来不是最好的朋友。幸好 GitHub 发布了 [GitHub for Windows](https://github.com/blog/1127-github-for-windows)，为 Windows 平台开发者提供了一个易于使用的 Git 图形客户端。

GitHub for Windows 是一个 Metro 风格应用程序，集成了自包含版本的 Git，bash 命令行 shell，PowerShell 的 posh-git 扩展。GitHub 为 Windows 用户提供了一个基本的图形前端去处理大部分常用版本控制任务，可以创建版本库，向本地版本库递交补丁，在本地和远程版本库之间同步。微软也通过 CodePlex 向开发者提供 git 版本控制系统，而 GitHub 现在创造了一个更具有吸引力的 Windows 版本。

对于windows用户来说也可以安装[msysgit](http://msysgit.github.com/)，这也是一个托管在github上的站点。

对于新手来说，在github上搭建Ruhoh基本上是follow-and-do。

1.在githhub上注册[免费帐号](https://github.com/signup/free)。

2.[host you blog](http://ruhoh.com/)，发布你的博客站点。
	
	1.设置你博客的 github 仓库
	2.发布你的博客站点

2. ruhoh本地安装

ruhoh 是一个 ruby 的套件，因此可通过 gem 來安装。在 Windows 平台下安装 Ruby 有几个选择。第一个选择是仅安装编译好的二进制文件。第二个选择是直接执行“一步安装”程序，假如您不知道如何安装 Ruby,[RubyInstaller](http://rubyinstaller.org/)将是您最好的选择。（这种安装方式除 Ruby 之外，捆绑一些额外的资源库。）你还需要先安装[ RubyInstaller DevKit](http://wiki.github.com/oneclick/rubyinstaller/development-kit)。

ruhoh gem依赖于：

    rack - for web-server integration
    directory_watcher - for watching files for updates in realtime.
    mustache - for templating.
    redcarpet - for Markdown parsing.
    nokogiri - for HTML handling and RSS support.

打开Git Shell(安装GitHub for windows时带的)，进去之后可以看到是windows powershell，但支持git等命令，我们可以查看当前安装过的ruby和gem的版本(这里假设安装到C:\Dev下)，并执行：

$gem install ruhoh 

![Alt text](/media/install ruhoh.png)

安装后可以输入以下命令确认是否安装成功。

$ruhoh help

本地运行Ruhoh:

$git clone git://github.com/ruhoh/bolg.git YujieDu.ruhoh.com

$cd YujieDu.ruhoh.com
$rackup -p 9292

然后打开浏览器访问http://localhost:9292/ 应该就能看到[默认页面。

现在把以上步骤记录到我的第一篇博客里：

$ ruhoh page Fun/2013-01-05-My-first-blog-ruhoh.md


收工！

Reference

    http://ruhoh.com/usage/
    http://ruhoh.com/how-it-works/

