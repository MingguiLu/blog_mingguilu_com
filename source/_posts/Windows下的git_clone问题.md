---
date: 2017-08-10T17:32:57+08:00
description: ""
tags: 
- Git
- git clone
- "warning: Clone succeeded, but checkout failed."
title: Windows下的git_clone问题
categories: 技术分享
photos: /images/photos/git_00.png
---

近期为了方便学习数据挖掘与数据分析，将系统由Linux Mint换回windows10开发者预览版，并安装了Ubuntu on Windows 10，由于硬盘格式化了，需要从Github上克隆博客代码，却遇到了问题。

<!--more-->

Windows10的分区挂载在Ubuntu on Windows 10的/mnt目录下，ls可以看到Windows的C、D两个分区

	mingguilu@DESKTOP-KME2TBI:~$ cd /mnt/
	mingguilu@DESKTOP-KME2TBI:/mnt$ ls
	c  d
	mingguilu@DESKTOP-KME2TBI:/mnt$

准备将博客代码克隆到D盘下，便于Windows和Ubuntu都能操作，但是克隆时却报错了：

	mingguilu@DESKTOP-KME2TBI:/mnt/d/helloworld/hugo$ git clone git@github.com:mingguilu/blog_mingguilu_com.git
	Cloning into 'blog_mingguilu_com'...
	remote: Counting objects: 522, done.
	remote: Compressing objects: 100% (5/5), done.
	remote: Total 522 (delta 0), reused 1 (delta 0), pack-reused 517
	Receiving objects: 100% (522/522), 16.24 MiB | 1.36 MiB/s, done.
	Resolving deltas: 100% (188/188), done.
	Checking connectivity... done.
	error: unable to create file content/猿/AutoIT3开发Helpdesk自动化工具之一:简介.md (Invalid argument)
	error: unable to create file content/猿/AutoIt3开发Helpdesk自动化工具之三:系统配置.md (Invalid argument)
	error: unable to create file content/猿/AutoIt3开发Helpdesk自动化工具之二:常用语法.md (Invalid argument)
	error: unable to create file content/猿/AutoIt3开发Helpdesk自动化工具之五:多任务组合.md (Invalid argument)
	error: unable to create file content/猿/AutoIt3开发Helpdesk自动化工具之四:软件安装.md (Invalid argument)
	fatal: unable to checkout working tree
	warning: Clone succeeded, but checkout failed.
	You can inspect what was checked out with 'git status'
	and retry the checkout with 'git checkout -f HEAD'

博客代码克隆成功了，但是content下多篇博文检出失败，为了排除Ubuntu on Windows 10的问题，在windows端Git bash上尝试克隆还是报错了......

一番折腾问题依旧，只好Google一下才大概明白是怎么回事，部分博文的文件名中含有冒号，之前都是在linux中创建的没有问题，但是Windows不支持文件名中包含特殊符号。

[Git can't checkout a repo from github](https://stackoverflow.com/questions/20715804/git-cant-checkout-a-repo-from-github) 

[Git pull error: unable to create file (Invalid argument)](https://stackoverflow.com/questions/26097568/git-pull-error-unable-to-create-file-invalid-argument) 

在Ubuntu on Windows 10上切换到家目录中，顺利克隆成功
	
	mingguilu@DESKTOP-KME2TBI:~$ git clone git@github.com:mingguilu/blog_mingguilu_com.git
	Cloning into 'blog_mingguilu_com'...
	remote: Counting objects: 522, done.
	remote: Compressing objects: 100% (5/5), done.
	remote: Total 522 (delta 0), reused 1 (delta 0), pack-reused 517
	Receiving objects: 100% (522/522), 16.24 MiB | 3.31 MiB/s, done.
	Resolving deltas: 100% (188/188), done.
	Checking connectivity... done.
