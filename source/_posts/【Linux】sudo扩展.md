---
title: 【Linux】sudo扩展
tags:
  - linux
  - sudo
  - sudoers
categories: 点滴随笔
photos: /images/photos/linux.jpg
date: 2018-08-21 21:40:51
description:
---

#### 配置文件/etc/sudoers

visudo 命令专门用来修改sudo配置文件/etc/sudoers，如果配置出错，会有提示，只有root账户才能使用

sudo权限控制的基本格式是

| 授权用户/组 | 允许的主机 | = |以谁的身份 | 是否免密码 | 可执行的命令 |
| :---: | :---: | :---: |:---: | :---: | :---: |
| 不以%号开头表示用户；以％开头表示用户组 | ALL表示所有；不为ALL表示主机或自定义的主机组 |　 | 可选，如省略相当于(root:root);(ALL)或者(ALL:ALL)表示提权到(任意用户:任意用户组) | NOPASSWD:　表示执行sudo时不需要密码验证 | ALL表示允许所有操作;或者用逗号分开的一系列命令 |

<!--more-->

#### 自定义配置文件/etc/sudoers/

sudo配置文件/etc/sudoers的内容十分敏感，直接影响系统安全，默认该文件为只读。当需要设置比较复杂的权限需求时，不推荐直接修改该文件，而是在/etc/sudoers.d目录下创建自定义的配置文件

默认/etc/sudoers文件已经启用了/etc/sudoers目录，见/etc/sudoers最后一行

    ## Read drop-in files from /etc/sudoers.d (the # here does not mean a comment)
    #includedir /etc/sudoers.d

在自定义的配置文件中，可使用User_Alias、Host_Alias、Runas_Alias和Cmnd_Alias来定义所需的字段

    User_Alias  NAME = User_List

    Host_Alias  NAME = Host_List
    
    Runas_Alias NAME = Runas_List
    
    Cmnd_Alias  NAME = Cmnd_List

#### 案例演示:创建logger用户并限定指定的命令操作指定的软件包和日志文件

生产中为了方便检查应用运行状态和故障排查，常需要给某些开发人员开放查看应用和日志，但是生产服务器上安全无小事，必修严格控制，仅给予满足需求的最低权限。

| UserName | APP_DIR | LOG_DIR | 
| :--- | :---: | :---: | 
| logger | /usr/apache-tomcat-7.0.69/webapps | /usr/apache-tomcat-7.0.69/logs |
| 仅用于查看应用软件包和日志 | 可以列出、校验、下载软件包 | 可以列出、读取、下载应用日志 |

##### 创建logger用户和组

    groupadd logger
    useradd -d /home/logger -g logger -s /bin/bash logger
    echo "000000" | passwd --stdin logger

##### 限定logger用户的shell环境

    vi /home/logger/.bash_profile

    #PATH=$PATH:$HOME/.local/bin:$HOME/bin

修改为：


    PATH=$HOME/bin

<!--more-->

##### 禁止logger用户操作环境变量

    cd /home/logger
    chown root:root .bash_profile .bashrc

###### 创建logger用户可用命令的软连接

    cd /home/logger
    mkdir bin
    chmod 755 bin/

    ln -s /bin/ls /home/logger/bin/ls
    ln -s /bin/cat /home/logger/bin/cat
    ln -s /usr/bin/head /home/logger/bin/head
    ln -s /usr/bin/tail /home/logger/bin/tail
    ln -s /bin/more /home/logger/bin/more
    ln -s /usr/bin/less /home/logger/bin/less
    ln -s /bin/grep /home/logger/bin/grep
    ln -s /bin/awk /home/logger/bin/awk
    ln -s /usr/bin/tac /home/logger/bin/tac
    ln -s /usr/bin/cksum /home/logger/bin/cksum
    ln -s /usr/bin/md5sum /home/logger/bin/md5sum
    ln -s /usr/bin/passwd /home/logger/bin/passwd
    ln -s /usr/bin/sudo /home/logger/bin/sudo
    ln -s /usr/bin/sz /home/logger/bin/sz

###### 限定操作命令和目录

    echo "User_Alias LOGGER = logger" > /etc/sudoers.d/01_logger
    echo "Host_Alias LOGGER = 172.29.20.1" > /etc/sudoers.d/01_logger
    echo "Cmnd_Alias LOGGER = /bin/ls /usr/apache-tomcat-7.0.69/logs/*, /bin/cat /usr/apache-tomcat-7.0.69/logs/*, /bin/grep /usr/apache-tomcat-7.0.69/logs/*, /bin/more /usr/apache-tomcat-7.0.69/logs/*, /usr/bin/less /usr/apache-tomcat-7.0.69/logs/*, /usr/bin/tail /usr/apache-tomcat-7.0.69/logs/*, /usr/bin/head /usr/apache-tomcat-7.0.69/logs/*, /usr/bin/tac /usr/apache-tomcat-7.0.69/logs/*, /usr/bin/sz /usr/apache-tomcat-7.0.69/logs/*, /bin/ls /usr/apache-tomcat-7.0.69/webapps/*, /usr/bin/md5sum /usr/apache-tomcat-7.0.69/webapps/*, /usr/bin/cksum /usr/apache-tomcat-7.0.69/webapps/*" >> /etc/sudoers.d/01_logger
    echo "LOGGER LOGGER=(root)  NOPASSWD: LOGGER" >> /etc/sudoers.d/01_logger

##### 设置登录告示

    echo "" >> /etc/motd
    echo "########## logger user can do like below ##########" >> /etc/motd
    echo "" >> /etc/motd
    echo "ls /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "cat /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "grep /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "more /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "less /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "tail /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "head /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "tac /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "sz /usr/apache-tomcat-7.0.69/logs/*" >> /etc/motd
    echo "ls /usr/apache-tomcat-7.0.69/webapps/*" >> /etc/motd
    echo "md5sum /usr/apache-tomcat-7.0.69/webapps/*" >> /etc/motd
    echo "cksum /usr/apache-tomcat-7.0.69/webapps/*" >> /etc/motd
    echo "" >> /etc/motd

##### logger用户登录后查看日志
   
    ########## logger user can do like below ##########

    ls /usr/apache-tomcat-7.0.69/logs/*
    cat /usr/apache-tomcat-7.0.69/logs/*
    grep /usr/apache-tomcat-7.0.69/logs/*
    more /usr/apache-tomcat-7.0.69/logs/*
    less /usr/apache-tomcat-7.0.69/logs/*
    head /usr/apache-tomcat-7.0.69/logs/*
    tail /usr/apache-tomcat-7.0.69/logs/*
    tac /usr/apache-tomcat-7.0.69/logs/*
    sz /usr/apache-tomcat-7.0.69/logs/*
    ls /usr/apache-tomcat-7.0.69/webapps/*
    md5sum /usr/apache-tomcat-7.0.69/webapps/*
    cksum /usr/apache-tomcat-7.0.69/webapps/*