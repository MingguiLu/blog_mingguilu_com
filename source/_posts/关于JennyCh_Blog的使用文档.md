---
title: 关于JennyCh_Blog的使用文档
date: 2017-02-14T11:57:04+08:00
description: ""
tags: 
- JennyCh
- hugo
- Github Pages 
- Markdown
categories: 技术分享
photos: /images/20170214/jennych_01.png

---

![](/images/20170214/hugo_github_markdown.png)

###  一  准备

####   1. Hugo

#####  简述:

Hugo是由Go语言实现的静态网站生成器。其特点是：简单、易用、高效、易扩展、快速部署。　

官方网站：[http://gohugo.io/](http://gohugo.io/)

入门推荐：[Hugo中文文档](http://www.gohugo.org/)

#####  安装:

[下载适用的windows的hugo_x.xx.x_Windows-64bit.zip](https://github.com/spf13/hugo/releases)

解压后重命名为`hugo.exe`并存放在c:\Program Files\hugo，并把路径添加到系统环境变量Path中，然后再cmd中输入`hugo version`测试是否可用

	>　hugo version
	Hugo Static Site Generator v0.16 BuildDate: 2016-06-06T08:33:34+08:00

<!--more-->

####  2.  Github pages　

##### 简述：

[Git](https://git-scm.com/)是一款免费、开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目

入门推荐：[廖雪峰Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)、[git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)

[GitHub](https://github.com/) 是一个面向开源及私有软件项目的托管平台，只支持 Git 作为唯一的版本库格式进行托管

[Github pages](https://pages.github.com/)用于将托管在Github上的项目发布位个人主页网站或项目主页网站

##### 安装Git

[下载Git for windows](https://git-scm.com/downloads)，以默认选项安装，在桌面双击运行`Git Bash`

##### 创建Github仓库

在`Repositories`页面点击`New`创建新仓库

![](/images/20170214/git_jenny_01.png)

以下是`JennyCh`托管在`Github`个人主页和博客项目的仓库

[blog](https://github.com/jennych/blog)		托管JennyCh Blog的静态博客网页的仓库

[jennych_blog_hugo](https://github.com/jennych/jennych_blog_hugo)		托管JennyCh Blog的站点源代码的仓库

[jennych.github.io](https://github.com/jennych/jennych.github.io)		托管JennyCh个人主页静态网页的仓库

[jennych_github_io](https://github.com/jennych/jennych_github_io)		托管JennyCh个人主页的站点源代码的仓库

#####  启用Github Pages

个人主页：例如[https://jennych.github.io](https://jennych.github.io) ，创建`Ｇithub帐号.github.io`的仓库，并上传个人主页静态页面项目即可

项目主页：例如[https://jennych.github.io/blog/](https://jennych.github.io/blog/) ，创建随意名称的仓库，并上传项目的静态页面，只要在根目录下存在index.html，即可开启Ｇithub Pages，`settings`－－>`Ｇithub Pages`－－>`Source`－－>`master branch`－－> `Save`，即可访问项目主页`https://Github帐号.github.io/项目仓库名/`

####  3. Markdown

Markdown 是一种轻量级的「标记语言」，它拥有很多优点，语法简洁明了、学习容易，而且功能比纯文本更强，导出格式随心所欲，因此有很多人用它写博客

入门推荐：[Markdown——入门指南](http://www.jianshu.com/p/1e402922ee32/)

[MarkdownPad](http://markdownpad.com/) 是Windows下的一个多功能Markdown编辑器

### 二　使用Hugo

创建Hugo静态站点

	hugo new site 站点名称

	$ hugo new site  my_website
	Congratulations! Your new Hugo site is created in "D:\\Hellolworld\\my_website".

Hugo站点下包含如下目录和文件：

* archetypes：存放创建新文章时自动生成内容的模板
* content：存放markdown格式的文章
* layouts：存放网站前端的html模板
* static：存放img、font、css、js等文件
* public: 存放生成的静态网页内容
* theme: 存放网站主题模板(一般将模板内容直接覆盖到根目录)
* config.toml: 网站的配置文件，一般包括baseurl、title、languageCode等配置参数

添加网站主题

	$ cd my_website/themes/

	$ git clone https://github.com/christianmendoza/hugo-smpl-theme
	Cloning into 'hugo-smpl-theme'...
	remote: Counting objects: 78, done.
	remote: Compressing objects: 100% (55/55), done.
	remote: Total 78 (delta 17), reused 78 (delta 17), pack-reused 0
	Unpacking objects: 100% (78/78), done.

	$ cd ..
	$ pwd
	/d/Hellolworld/my_website

	$ ls themes/hugo-smpl-theme/
	archetypes/   images/   LICENSE    static/
	exampleSite/  layouts/  README.md  theme.toml

	$ cp themes/hugo-smpl-theme/exampleSite/config.toml  .
	# 使用theme文件时，需要适配的config.toml(一般在theme根目录或exampleSite目录下可以找到)，并指定theme = "theme-name"参数才能启动网站

新建文章

	hugo new  路径/文章名称.md

	$ hugo new post/技术文章/第一篇技术文章.md
	D:\Hellolworld\my_website\content\post\技术文章\第一篇技术文章.md created

启动Hugo静态站点

	hugo server -t=主题名称
	# 如果已将theme文件覆盖到站点根目录，无需使用-t指定主题文件，hugo server即可

	$ hugo  server  -t=hugo-smpl-theme   -w
	Started building site
	0 of 1 draft rendered
	0 future content
	0 pages created
	0 non-page files copied
	0 paginator pages created
	0 tags created
	0 categories created
	in 7 ms
	Watching for changes in D:\Hellolworld\my_website\{data,content,layouts,static,themes}
	Serving pages from memory
	Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
	Press Ctrl+C to stop

生成静态页面

	# hugo命令用于将当前站点转换为静态页面，并自动保存在public目录下

	$ hugo
	Started building site
	0 of 1 draft rendered
	0 future content
	1 pages created
	0 non-page files copied
	0 paginator pages created
	0 tags created
	0 categories created
	in 46 ms

	$ ls public/
	404.html  assets/  index.html  index.xml  post/  sitemap.xml

### 三　使用Git 克隆和提交代码

以下内容以JennyCh Blog的站点为例

#### 1. 从Github远程仓库克隆JennyCh Blog的站点源代码

	git clone Ｇithub远程仓库地址
	#Github远程仓库地址分为两种：https和ssh ，可以在仓库主页中点开"Clone or Download"，点击`Use Https`或`Use SSH`可分别查看仓库地址

![](/images/20170214/git_jenny_02.png)

	$ git clone git@github.com:jennych/jennych_blog_hugo.git
	Cloning into 'jennych_blog_hugo'...
	remote: Counting objects: 151, done.
	remote: Compressing objects: 100% (131/131), done.
	remote: Total 151 (delta 10), reused 151 (delta 10), pack-reused 0
	Receiving objects: 100% (151/151), 4.25 MiB | 124.00 KiB/s, done.
	Resolving deltas: 100% (10/10), done.

	$ cd jennych_blog_hugo/

	$ ls
	archetypes/  content/      images/   LICENSE.md  static/
	config.toml  exampleSite/  layouts/  README.md   theme.toml

#### 2. 从Github远程仓库克隆JennyCh Blog的静态网页

	git clone Ｇithub远程仓库地址 public
	# 将JennyCh Blog的静态页面仓库克隆到本地，并重命名为public

	$ git  clone  git@github.com:jennych/blog.git  public
	Cloning into 'public'...
	remote: Counting objects: 169, done.
	remote: Compressing objects: 100% (113/113), done.
	remote: Total 169 (delta 41), reused 169 (delta 41), pack-reused 0
	Receiving objects: 100% (169/169), 3.84 MiB | 124.00 KiB/s, done.
	Resolving deltas: 100% (41/41), done.

	$ ls public/
	404.html  categories/  fancybox/  font/       img/        js/    sitemap.xml
	about/    css/         feed.xml   highlight/  index.html  post/  tags/

#### 3. 推送本地Git版本至Github远程仓库

当修改站点源代码或文章内容后，将修改内容提交为新版本，并推送到Github远程仓库jennych_blog_hugo

	$ pwd
	/d/Hellolworld/jennych_blog_hugo

	$ git add -A
	# 把修改内容提交到Git缓存区

	$ git commit -m "xxxx"
	# 把Git缓存区的内容提交为Git新版本，`-m "xxxx" `为本次提交的版本的简述，如果不加`-m`参数，会进入编辑描述信息的状态

	$ git push origin master
	# 将本地新版本推送到Github远程仓库

生成新的静态页面后提交为新版本，并推送到Github远程仓库blog

	$ hugo
	# 将修改后的代码和文章转换生成新的静态页面
	Started building site
	0 draft content
	0 future content
	6 pages created
	0 non-page files copied
	0 paginator pages created
	3 tags created
	2 categories created
	in 668 ms

	$ cd public/
	# 进入public目录,public目录存放着转换生成后的静态页面

	$ pwd
	/d/Hellolworld/jennych_blog_hugo/public

	$ git add -A
	# 把新生成的内容提交到Git缓存区

	$ git commit -m "xxxx"
	# 把Git缓存区的内容提交为Git新版本，`-m "xxxx" `为本次提交的版本的简述，如果不加`-m`参数，会进入编辑描述信息的状态

	$ git push origin master
	# 将本地新版本推送到Github远程仓库

推送完成，访问[https://jennych.github.io/blog/](https://jennych.github.io/blog/)即可看到新内容
