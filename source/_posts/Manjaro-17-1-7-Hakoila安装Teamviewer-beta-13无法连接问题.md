---
title: Manjaro_17.1.7_Hakoila安装Teamviewer-beta_13无法连接问题
tags:
  - manjaro
  - teamviewer
categories: 趣玩尝新
photos: /images/photos/teamviewer.jpg
date: 2018-04-12 00:11:54
description:
---

#### linux系统信息如下：

    $ lsb_release -a
    LSB Version:	n/a
    Distributor ID:	ManjaroLinux
    Description:	Manjaro Linux
    Release:	17.1.7
    Codename:	Hakoila

#### 仓库中可用的Teamviewer版本

    $ sudo pacman -Ss teamviewer
    archlinuxcn/teamviewer 12.0.90041-7
        All-In-One Software for Remote Support and Online Meetings
    archlinuxcn/teamviewer-beta 13.1.3026-1
        All-In-One Software for Remote Support and Online Meetings - beta version

<!--more-->

#### 安装运行Teamviewer-12之后频繁掉线，不可用

    $ sudo pacman -S teamviewer
    正在解决依赖关系...
    正在查找软件包冲突...

    软件包 (1) teamviewer-12.0.90041-7

    全部安装大小：  145.02 MiB

    :: 进行安装吗？ [Y/n] y

    ......

![](/images/20180412/20180412_01.png)

#### 安装运行Teamviewer-beta-13之后无连接，不可用

    $ sudo pacman -S teamviewer-beta
    正在解决依赖关系...
    正在查找软件包冲突...

    软件包 (1) teamviewer-beta-13.1.3026-1

    全部安装大小：  59.26 MiB

    :: 进行安装吗？ [Y/n] y

    ......

![](/images/20180412/20180412_02.png)

#### 使用官网下载tar包直接运行后无连接，不可用

Teamviewer官方下载页面： [https://www.teamviewer.com/en/download/linux/](https://www.teamviewer.com/en/download/linux/)


    $ tar xf teamviewer_13.1.3026_amd64.tar.xz 
    $ cd teamviewer 
    $ ll
    总用量 28K
    drwx------ 2 mingguilu mingguilu 4.0K 4月  12 00:52 config
    drwxr-xr-x 2 mingguilu mingguilu 4.0K 3月  20 20:11 doc
    drwx------ 2 mingguilu mingguilu 4.0K 4月  12 00:39 logfiles
    -rwxr-xr-x 1 mingguilu mingguilu  952 3月  20 20:11 teamviewer
    -rw-r--r-- 1 mingguilu mingguilu  285 4月  12 00:39 teamviewer.desktop
    drwxr-xr-x 6 mingguilu mingguilu 4.0K 3月  20 20:11 tv_bin
    -rwxr-xr-x 1 mingguilu mingguilu  956 3月  20 20:11 tv-setup
    $ ./teamviewer 
    Init...
    CheckCPU: SSE2 support: yes
    Checking setup...
    Launching TeamViewer ...
    Starting network process (no daemon)
    Network process already started (or error)
    Launching TeamViewer GUI ...

![](/images/20180412/20180412_02.png)

#### 使用官网下载tar包安装后重启系统，可正常使用

查看tv-setup的用法：

    $ sudo ./tv-setup 
    [sudo] mingguilu 的密码：

    How to use TeamViewer (tar.xz)        

    teamviewer                           run teamviewer directly 
    You can just extract the tar.xz package and run 'teamviewer' without installation.
    It will behave similar to a TeamViewer Portable or QuickSupport on Windows.
    This should work if all necessary libraries (packages) are installed on your system.

    tv-setup checklibs                   identify missing libraries 
    Run this command to identify missing libraries
    You can then look for the matching packages and install them manually.

    tv-setup install                     interactive installation 
    tv-setup install force               no questions 
    A permanent installation with all the features of the RPM/DEB packages
    (start menu entries, auto start, background daemon for permanent access)

    tv-setup uninstall [force]           undo previous (TAR) installation 
    Removes the package. Log files and configuration are not removed

进行安装：

    $ sudo ./tv-setup install        

        -=-   TeamViewer tar.xz interactive installation   -=-      

    Checking dependencies                 

    Analyzing dependencies ...            

      All library dependencies (*.so) seem to be satisfied!

      QtQuickControls seems to be missing
    Serious Problem - installation should be aborted
    Missing libraries                     
        TeamViewer will not be operational without these libraries. Please install them and try again.
        Continue (y) or abort (n) ?  [Y/n]? y

    Installing files...                   
        Files will be installed in '/opt/teamviewer'
        Continue (y) or abort (n) ?  [Y/n]? 

    Copying files...                      
      done

    Install daemon?                       
        Note: You can (un)install the daemon at any time.
        Commands are explained in 'teamviewer help'
        Continue (y) or skip (n) ?  [Y/n]? y
    Create menu entries?                  
        Creates menu entries for your desktop environment.
        Continue (y) or skip (n) ?  [Y/n]? y
    gtk-update-icon-cache: Cache file created successfully.
      Done!
        TeamViewer TAR has been sucessfully installed.
        Run teamviewer help for more information.

![](/images/20180412/20180412_03.png)


---

#### 参考文档

* 《manjaro/arch Linux 安装teamviewer13后无法正常价运行 》：[https://blog.csdn.net/vectorwww/article/details/79388281](https://blog.csdn.net/vectorwww/article/details/79388281)


