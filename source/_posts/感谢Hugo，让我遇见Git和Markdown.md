---
title: 感谢Hugo，让我遇见Git和Markdown
date: 2017-01-14T20:33:09+08:00
tags:
- hugo
- git
- markdown
categories: 思考感悟
description: 
photos: /images/20170114/gan_00.png
---

去年7月在机缘巧合之下接触了静态博客的概念，深入了解了一些知识之后，特别欣喜，决定动手搭建一个自己的静态博客。时间一晃到了年底，当着手实现的时候发现并不简单

首先是选择哪一款生成器，在知乎上研究一番后，最终在Jekyll、Hexo和Hugo中，选择了Hugo，因为简单、生成速度快，在[静态博客生成器排名榜](https://staticsitegenerators.net/)上发展势头好，接着在《[Hugo中文文档](http://www.gohugo.org)》学习如何安装使用它，利用自己有限的网页知识在Hugo作者个人博客[spf13.com](http://sfp13.com)的源码基础上定制了自己的博客前端
 
然后编写文档需要学习一门新的文本标记语言Markdown，所幸Markdown语法相对容易，常用的也不多，看完Te_Lee 分享的《[认识与入门 Markdown](http://sspai.com/25137/)》和《[Markdown——入门指南](http://www.jianshu.com/p/1e402922ee32/)》，就可以入门了

最后为了将Hugo发布到Github Pages的个人主页上，必须得学习Git。我并非一名程序，虽然之前使用过svn和github，但并不了解这个强大并让我之后受益匪浅的Git。花了大概一周的时间潜心学习了廖学锋老师的[《Git教程》](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)和贺永康老师的[《深入理解Git》
](http://edu.51cto.com/course/course_id-1838.html)

经过1个月的折腾，终于把博客上线了。然而，收获不仅是有了这个博客，还促使自己学习了Markdown和Git，更重要的是，Git的版本控制思想让我受益匪浅！

<!--more-->

为了记录博客搭建的过程，除了写文档，还用git版本记录代码改动，经过实践发现很多问题：

* 前端代码经常改了又改，有时仅仅1处字体px大小；有时修改bug到一半不得要领，先去解决另外一个，这样导致频繁的`git commit`，且一个`commit`掺和多处修改

* 因为频繁的`git commit`，`commit message`每次随性而写，回头`git log`一看全是一句话概括，具体改了什么自己也看不懂了

![](/images/20170114/gan_01.png)

* 当写文档时，前一篇没写完搁置了，接着完成了另外一篇，这时commit并push就很尴尬了

有时候不得不感慨，真是想什么来什么！这段时间[伯乐在线官方微博](http://weibo.com/jobbole)刚好推荐了两篇git干货，[《Git 最佳实践：commit msg》](http://blog.jobbole.com/109197/)和[《Git 最佳实践：分支管理》](http://blog.jobbole.com/109466/)。虽然自己没法全部理解两篇文章的内容，但依然指导我做些改变

* 多使用git分支，在新分支里开始一篇文档，在新分支里修改前端的代码，当文档完成可以发表，或代码修改完测试没有问题，再将新分支合并到`marst`分支，并`push`到github的远程仓库里

* 基于以上的分支，`commit`即可灵活很多，坚持一次`commit`只完成一个任务，并确保每次`commit`的正确性。因为“每次提交的代码都可能产生一个可发布的版本，坚持这个实践，那么软件会一直处于**可用状态** —— 《持续交付》”

* 在[《Git 最佳实践：commit msg》](http://blog.jobbole.com/109197/)关于`commit message`规范的指导下，重新创建了版本库，并在写message时尝试写简单易懂的标题和详尽明了的内容

![](/images/20170114/gan_02.png)

近期刚好在看一本关于Devops的书《持续交付 - 发布可靠软件的系统方法》，本书前2章着重讲述了使用版本控制对构建软件可重复且可靠的自动化发布的重要意义。而这次git的实践，有助于理解书中的内容，最终使用“Continuous Delivery”作为博客标题也是因为这本书。
