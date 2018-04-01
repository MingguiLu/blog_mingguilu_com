---
date: 2017-02-26T17:32:57+08:00
description: ""
tags:
- Github Pages
- Coding Pages
- GitCafe
title: Github_Pages灾备至Coding_Pages
categories: 趣玩尝新
photos: /images/20170226/170226_01_02.jpg
---

周四晚间在家访问我托管在Github Pages上的Blog时发现网站打不开，确认本地网络没问题，尝试清理浏览器缓存，刷新系统缓存，问题依旧。下意识的回想起来今天我修改代码啦？调整过DNSPod的解析记录啦？还是站点没备案被和谐了？......打开微博刷新了一下，看到关注的几个技术大V:Easy、刘巍峰、梁斌penny都发布或转发了Github无法访问的消息，我有点惊讶：卧槽！why？

<!--more-->

随即查看了DNSPod的监控，看到托管在Github Pages的三个站点全部告警。

![](/images/20170226/170226_01_01.jpg)

零点前后部分地区开始恢复访问，但周五上午我在工地访问Github依然时断时续，速度极慢，而github.io始终无法访问，直到中午才恢复正常。事后没能找到关于本次Github无法访问的详细解析，但发现相对我这样的Git小白用户，真正的开发人员似乎都见怪不怪，这次只是又一次而已。

我想起来刚接触Hugo时在[Hugo中文文档](http://www.gohugo.org/)看到的那篇[通过webhook将Hugo自动部署至GitHub Pages和GitCafe Pages](http://www.gohugo.org/post/coderzh-automated-deploy-hugo/)，那时完全看不懂，其实现在也是...... 不过我突然明白为什么托管在Github上的代码也要容灾，虽然Github的服务相对稳定可靠，但我们的网络并非如此，将代码备份到国内的代码托管服务上是很有必要的。目前我并不需要自动部署项目至Github和GitCafe，我只希望能够将本地代码同时push到两个远程库，手工创建版本库，并推送到远程仓库，可以让我更熟练Git的常用命令。

这个方法很简单：先将Github上已有的仓库导入到Coding，在本地.git版本库配置文件中添加Coding远程仓库的地址，一次push即可将代码同时推送到Github和Coding。

### 1. 注册Coding

起初我是冲着[GitCafe](https://gitcafe.com/)去的，但现在已经合并到[Coding](https://coding.net)了。

![](/images/20170226/170226_01_01_01.png)

### 2. 创建项目的同时导入Github的仓库

![](/images/20170226/170226_01_02_01.png)

### 3. 本地.git版本库追加Coding远程仓库地址

修改 .gitmodules 

	$ vim .gitmodules 
	[submodule "public"]
		path = public
		url = git@github.com:mingguilu/blog.mingguilu.com.git
		url = git@git.coding.net:Mingguilu/blog.mingguilu.com.git		# 添加Coding远程仓库地址

修改 .git/config

	$ vim .git/config 
	[core]
		repositoryformatversion = 0
		filemode = true
		bare = false
		logallrefupdates = true
	[remote "origin"]
		url = git@github.com:mingguilu/blog_mingguilu_com.git
		url = git@git.coding.net:Mingguilu/blog_mingguilu_com.git		# 添加Coding远程仓库地址
		fetch = +refs/heads/*:refs/remotes/origin/*
	[branch "master"]
		remote = origin
		merge = refs/heads/master
	[submodule "public"]
		url = git@github.com:mingguilu/blog.mingguilu.com.git
		url = git@git.coding.net:Mingguilu/blog.mingguilu.com.git		# 添加Coding远程仓库地址

修改 .git/modules/public/config

	$ vim .git/modules/public/config 
	[core]
		repositoryformatversion = 0
		filemode = true
		bare = false
		logallrefupdates = true
		worktree = ../../../public
	[remote "origin"]
		url = git@github.com:mingguilu/blog.mingguilu.com.git
		url = git@git.coding.net:Mingguilu/blog.mingguilu.com.git		# 添加Coding远程仓库地址
		fetch = +refs/heads/*:refs/remotes/origin/*
	[branch "master"]
		remote = origin
		merge = refs/heads/master

### 4. 至此，一次push即可同时推送到Github和Coding

	$ git push  origin master 
	对象计数中: 13, 完成.
	Delta compression using up to 4 threads.
	压缩对象中: 100% (13/13), 完成.
	写入对象中: 100% (13/13), 1.68 MiB | 368.00 KiB/s, 完成.
	Total 13 (delta 5), reused 0 (delta 0)
	remote: Resolving deltas: 100% (5/5), completed with 5 local objects.
	To git@github.com:mingguilu/blog.mingguilu.com.git			# 推送至Github远程仓库
	   c5e9106..ebb5fca  master -> master
	对象计数中: 13, 完成.
	Delta compression using up to 4 threads.
	压缩对象中: 100% (13/13), 完成.
	写入对象中: 100% (13/13), 1.68 MiB | 0 bytes/s, 完成.
	Total 13 (delta 5), reused 0 (delta 0)
	To git@git.coding.net:Mingguilu/blog.mingguilu.com.git		# 推送至Coding远程仓库
	   c5e9106..ebb5fca  master -> master

### 5. 开启Coding仓库的Pages服务并绑定域名

![](/images/20170226/170226_01_05_01.png)

### 6. 在DNSPod的增加域名别名解析记录至pages.coding.me.，并设置为默认线路，xxx.github.io设置为国外线路

到这里，已完成将Github仓库导入到Coding项目作为备份，并实现Blog的在线访问的灾备，和国内访问速度的优化。








