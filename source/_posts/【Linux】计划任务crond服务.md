---
title: 【Linux】计划任务atd和crond服务
tags:
  - Linux
  - atd
  - crond
  - 计划任务
categories: 点滴随笔
photos: 
date: 2018-08-16 16:33:27
description:
---

#### 一次性计划任务at

##### 安装at
CentOS 7.4 默认没有安装at，需自行安装并启动atd服务

    yum install -y at
    systemctl start atd

##### 命令语法

| 命令 | 用途 |
| :--- | :--- |
| at 时间 | 默认交互式创建一次性计划任务,Ctrl+d组合键结束编辑 |
| at -l | 查看已设置但还未执行的一次性计划任务 |
| atq | 作用同at -l |
| atrm 任务序号 | 删除已设置但还未执行的一次性计划任务 |

<!--more-->

##### 示例

    # date
    Thu Aug 16 16:54:13 CST 2018
    
    # at 17:00
    at> cp -a /data/helloworld /data/backup/helloworld.$(date +%Y%m%d-%H:%M:%S).bak
    at> <EOT>
    job 5 at Thu Aug 16 17:00:00 2018
    
    # at -t "201808161702"
    at> cp -a /data/helloworld /data/backup/helloworld.$(date +%Y%m%d-%H:%M:%S).bak
    at> <EOT>
    job 6 at Thu Aug 16 17:02:00 2018
    
    # echo "cp -a /data/helloworld /data/backup/helloworld.$(date +%Y%m%d-%H:%M:%S).bak" | at 17:05
    job 7 at Thu Aug 16 17:05:00 2018
    
    # at 17:10
    at> cp -a /data/helloworld /data/backup/helloworld.$(date +%Y%m%d-%H:%M:%S).bak
    at> <EOT>
    job 8 at Thu Aug 16 17:10:00 2018

    # at -l
    5	Thu Aug 16 17:00:00 2018 a root
    6	Thu Aug 16 17:02:00 2018 a root
    7	Thu Aug 16 17:05:00 2018 a root
    8	Thu Aug 16 17:10:00 2018 a root

    # atrm 8

    # atq
    5	Thu Aug 16 17:00:00 2018 a root
    6	Thu Aug 16 17:02:00 2018 a root
    7	Thu Aug 16 17:05:00 2018 a root
    
    # ls
    helloworld.20180816-16:55:48.bak  helloworld.20180816-17:00:00.bak  helloworld.20180816-17:02:00.bak

以上用不同的语法创建了4个一次性计划任务，删除了1个，都执行成功了，但看到最后一个计划任务拷贝的备份文件名称是helloworld.20180816-16:55:48.bak时，诧异了几秒钟，不应该是helloworld.20180816-17:05:00.bak才对吗？！？ 不过很快明白过来了，echo “....$(date +%Y%m%d-%H:%M:%S).bak” 执行的那一刻就已经将当时系统时间取值了

#### 长期性计划任务cron

CentOS 7.4 默认启用crond服务

##### 命令语法

| 命令 | 用途 |
| :--- | :--- |
| crontab -e | 创建、编辑计划任务 |
| crontab -l | 查看当前计划任务 |
| crontab -r | 删除crontab文件 |
| crontab -u | root下编辑其他用户的计划任务 |

##### 参数格式

cron计划任务参数格式：

| 分钟 | 小时 | 日 | 月 | 星期 | 命令 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0~59的整数 | 0~23的任意整数 | 1~31的任意整数 | 1~12的任意整数 | 0~7的任意整数，0和7均为周日 | 要执行的命令或脚本 |
| 必须要有数值，决不能为空或*， /除号表示间隔时间 | ，逗号表示多个时间段，通用 | -减号表示连续的时间周期，通用 | ， - 通用 | 日 和 星期 不能同时使用，否则会冲突 |  |

##### 示例一

    # cat /data/scripts/backup.sh 
    #!/bin/bash
    /usr/bin/cp -a /data/helloworld /data/backup/helloworld.$(date +%Y%m%d-%H:%M:%S)

    # crontab -e
    crontab: installing new crontab

    # crontab -l
    */1 * * * * sh /data/scripts/backup.sh  #每分钟备份一次/data/helloworld至/data/backup

    # ll /data/backup/
    total 0
    drwxr-xr-x 2 root root 276 Aug 16 14:33 helloworld.20180816-18:44:01
    drwxr-xr-x 2 root root 276 Aug 16 14:33 helloworld.20180816-18:45:01
    drwxr-xr-x 2 root root 276 Aug 16 14:33 helloworld.20180816-18:46:01
    drwxr-xr-x 2 root root 276 Aug 16 14:33 helloworld.20180816-18:47:01
    drwxr-xr-x 2 root root 276 Aug 16 14:33 helloworld.20180816-18:47:01

    # crontab -r
    # crontab -l
    no crontab for root

##### 示例二：root用户编辑其他用户的计划任务

    # crontab -e -u itadmin 
    crontab: installing new crontab

    # crontab -l -u itadmin
    #每分钟检查一次内存用量
    */1 * * * * /usr/bin/free -m > /home/itadmin/checkMem/$(/bin/date +\%Y\%m\%d-\%H:\%M:\%S).log

    # ll /home/itadmin/checkMem
    total 112
    -rw-r--r-- 1 itadmin itadmin 204 Aug 16 19:19 20180816-19:19:01.log
    -rw-r--r-- 1 itadmin itadmin 204 Aug 16 19:20 20180816-19:20:01.log
    -rw-r--r-- 1 itadmin itadmin 204 Aug 16 19:21 20180816-19:21:01.log
    -rw-r--r-- 1 itadmin itadmin 204 Aug 16 19:23 20180816-19:23:01.log
    -rw-r--r-- 1 itadmin itadmin 204 Aug 16 19:24 20180816-19:24:01.log

##### 注意事项

* crontab文件路径位于/var/spool/cron/，以用户名称来命名，直接编辑该文件也生效
* crontab中参数字段不需要设置的，使用星号*占位
* crontab可以以#号开头写上注释信息，便于快速理解任务的信息
* crontab参数中所有的命令用绝对路径方式来写，避免用户未加载的环境变量导致任务失败
* 可以用 whereis 来查询命令的绝对路径
* crontab系统变量需要使用反斜杠转义,如上 $(/bin/date +\%Y\%m\%d-\%H:\%M:\%S).log

