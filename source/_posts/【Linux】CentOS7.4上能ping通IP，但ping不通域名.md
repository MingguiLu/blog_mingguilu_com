---
title: 【Linux】CentOS7.4上能ping通IP，但ping不通域名
tags:
  - Linux
  - network
  - ip
categories: 点滴随笔
photos: /images/photos/wtf.png
date: 2018-08-23 16:29:41
description:
---

这是一个马虎大意+先入为主的沙雕故事

偶然一次查看CentOS IP信息时，赫然发现本地网卡多了一个IP，但我笃定该台机器只配置一个固定IP：172.29.23.3，现在却显示为`scope global secondary eth0`,然后多出一个IP：172.29.23.88

![](/images/20180904/20180904_01.png)

当时就先入为主的认为自己确实只配置了一个固定IP，并且之前没发现这个问题，肯定是谁动了配置了。。。

<!--more-->

其实当时只要稍微冷静一点就能发现问题， inet 172.29.23.88/24 brd 172.29.23.255 scope global dynamic eth0 ，说明这个iP是通过dhcp自动获取的，检查一下网卡配置文件显示`BOOTPROTO="dhcp"`,果不其然。。。

![](/images/20180904/20180904_02.png)

于是抬手就修改了配置`BOOTPROTO="static"`，并重启了网卡，然后ssh连不上了。。。在其他机器上测试能ping通，telent 22端口也能通

![](/images/20180904/20180904_03.png)

然后只好远程桌面到hyper-v所在服务器上去调试CentOS的虚拟机，经反复检查和测试，确认网卡信息配置无误、DNS地址配置正确、默认路由正确，能ping通外网IP，但ping不通外网域名。。。

![](/images/20180904/20180904_05.png)

![](/images/20180904/20180904_04.png)

A few hours later 。。。 终于google了一剂猛药：[《Linux能ping通IP ping不通域名》](http://www.it165.net/os/html/201408/9093.html)

在防火墙开放53端口

![](/images/20180904/20180904_06.png)

然后恢复正常了

![](/images/20180904/20180904_07.png)
