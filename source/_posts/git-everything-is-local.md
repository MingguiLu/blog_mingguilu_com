---
date: 2016-12-08T08:48:05+08:00
description: ""
tags:
- git
- gitbash
title: Git-everything-is-local
categories: 技术分享
photos: /images/photos/git_00.png
---

### 1. 安装git
#### windows
下载并安装[Git for Windows](https://git-scm.com/downloads)，默认选项安装到Ajusting your PATH environment时，勾选第二项Run Git from Windows Command Prompt，即可在windows命令行使用git命令，不过Git Bash用户体验更好

#### ubnutu

	sudo apt-get install git

<!--more-->

### 2. 全局配置git
命令行

	git config --global user.name [your-name]
	git config --global user.email [your-email-address]
	git config --global color.ui true

配置文件

	vim ~/.gitconfig

查看全局配置

	git config --list

### 3. 创建repository/repo
创建本地git repo

	git init

克隆线上的项目

	git clone https://github.com/[github-account]/[project-name].git

### 4. 添加及提交文件
![](/images/20161208/git_3_01.png)

添加文件到staying area

	git add [file-name]

提交改动到git repository

	git commit -m ["commit-description"]

直接从working directory提交到git repository

	git commit -am ["commit-description"]

### 5. 查看git状态
查看git状态

	git status

查看git状态标志

	git status -s

标志的说明

	A: 你本地新增的文件（服务器上没有）

	C: 文件的一个新拷贝

	D: 你本地删除的文件（服务器上还在）

	M: 文件的内容或者mode被修改了

	R: 文件名被修改了

	T: 文件的类型被修改了

	U: 文件没有被合并(你需要完成合并才能进行提交)

	X: 未知状态(很可能是遇到git的bug了，你可以向git提交bug report)

忽略~结尾的文件

	echo "*~" > .gitignore

### 6. 查看文件区别
![](/images/20161208/git_6_01.png)

查看working directory 和 staying area 之间的区别

	git diff

查看staying area 和 git repository 之间的区别

	git diff --staged

查看history 和 working directory 之间的区别

	git diff HEAD

使用--stat参数查看简短信息

	git diff --stat [--staged|HEAD]

### 7. 撤销误操作
![](/images/20161208/git_7_01.png)

从git repository 撤销到staying area

	git reset [file-name]

从staying area撤销到working directory

	git checkout [file-name]

从git repository 撤销到working directory

	git checkout HEAD [file-name]


### 8. 移除及重命名文件
删除git文件

	git rm [file-name]

删除git文件，但保留源文件

	git rm --cached [file-name]

重命名git文件

	git mv [file-name] [file-name]


### 9. 暂存工作区
放入暂存区

	git stash

查看暂存区

	git stash list

恢复暂存区

	git stash pop

### 10. 图解commit对象
![](/images/20161208/git_10_01.png)

查看commit记录

	git log

或

	git log --pretty=oneline

或

	git log --oneline

或

	git reflog

查看commit对象类型

	git cat-file -t [HEAD|short-Hash]

commit对象类型说明

	tree 目录结构
	blob 一个二进制文件
	commit	一次提交的信息（包含tree、parent、author、committer信息）
	tag 标签（commit的别名）

查看commit对象详细信息

	git cat-file -p [HEAD|short-Hash]

### 11. 理解tree-ish表达式
查看commit对象指向的HASH值

	git rev-parse [HEAD|HEAD~|HEAD~2|master~3|master~4|...]

查看指定commit的tree的HASH值

	git rev-parse HEAD~4^{tree}

查看指定commit的blob的HASH值

	git rev-parse HEAD~6:[file-path]

### 12. 创建及删除分支
分支文件路径

	/.git/refs/heads/

查看所有分支

	git branch

创建分支

	git branch [branch-name]

切换分支

	git checkout [branch-name]

创建并切换分支

	git checkout -b [branch-name]

删除分支

	git branch -d [branch-name]

合并分支

	git merge [branch-name]

### 13. 配置Github远程仓库
创建SSH Key

	ssh-keygen -t rsa -C "youremail@example.com"

一路回车使用默认值，完成之后在用户主目录中打开 `.ssh`目录，可以看到

	id_rsa		私钥，不能泄露出去
	id_rsa.pub	公钥，可告诉任何人

登录Github，依次点击"settings"——>"SSH and GPG keys"——>"New SSH Key"
![](/images/20161208/git_13_01.png)

填写Title，在Key文本框里粘贴`id_rsa.pub`文件的内容，点击"Add SSH Key"

### 14. 同步远程Github仓库
关联本地仓库与远程仓库

	git remote add origin git@github.com:[github-account]/[repo-name].git

第一次把本地库master分支推送到远程库

	git push -u origin master

之后本地做了提交，即可推送到远程库

	git push origin master

### 15. 从Github远程库克隆
创建Github Repositories远程库
![](/images/20161208/git_15_01.png)

把远程库克隆到本地

	git clone git@github.com:[github-account]/[repo-name].git

或

	git clone https://github.com/[github-account]/[repo-name].git
