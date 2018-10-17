---
title: 【Linux】RedHat6.4使用yum时提示需注册问题
tags:
  - 标签1
  - 标签2
  - 标签3
categories: 运维之道 | 编程之路 | 趣玩尝新 | 若有所思 | 点滴随笔
photos: 
date: 2018-09-30 13:43:30
description:
---

近期在新工地需要遇到了许多历史悠久的项目，系统可以追溯到RHEL6.4、6.2和5.6，Apache2.2，JDK版本甚至远至1.4，之前运维项目大多是CentOS6.6或7.0以上，Redhat在生产上更是少用到，虽然与CentOS差别不大，为了安全生产还是要多动手练练

至于为什么老版本不升级，思量之下应该不外乎两个原因吧：

* 维稳，稳定大于一切。用前辈的说话：懒。
* 某些功能模块严重依赖于小版本的特性，牵一发动全身，参考《凤凰项目》

#### yum install遇到注册问题

安装好了Redhat6.4之后，需要装一些常用的工具，执行了一条yum install就遇到问题了

    # yum install -y telnet
    Loaded plugins: product-id, security, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to re
    Setting up Install Process
    No package telnet available.

<!--more-->

#### 卸载Redhat自带的yum工具

    # rpm -qa |grep yum | xargs rpm -e --nodeps

#### 下载yum相关的rpm包

    python-iniparse-0.3.1-2.1.el6.noarch.rpm
    yum-3.2.29-40.el6.centos.noarch.rpm
    yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
    yum-plugin-fastestmirror-1.1.30-14.el6.noarch.rpm

网上搜一下很多wget下载的链接，经实测，

    #链接有效，可以下载：
    wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
    wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm


    # 链接无效，无法下载：
    wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-3.2.29-40.el6.centos.noarch.rpm
    wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.30-14.el6.noarch.rpm

百度云网盘下载链接： https://pan.baidu.com/s/1wDbfBfUgoX3OvpFkB1velw 提取码: 27fk 

#### 安装rpm包

    # rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm 
    warning: python-iniparse-0.3.1-2.1.el6.noarch.rpm: Header V3 RSA/SHA256 Signature, key ID c105b9de: NOKEY
    Preparing...                ########################################### [100%]
      package python-iniparse-0.3.1-2.1.el6.noarch is already installed

    # rpm -ivh yum-metadata-parser-1.1.2-16.el6.x86_64.rpm 
    warning: yum-metadata-parser-1.1.2-16.el6.x86_64.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
    Preparing...                ########################################### [100%]
      1:yum-metadata-parser    ########################################### [100%]

    # rpm -ivh yum-3.2.29-40.el6.centos.noarch.rpm yum-plugin-fastestmirror-1.1.30-14.el6.noarch.rpm 
    warning: yum-3.2.29-40.el6.centos.noarch.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
    Preparing...                ########################################### [100%]
      1:yum-plugin-fastestmirro########################################### [ 50%]
      2:yum                    ########################################### [100%]

#### 替换yum源

    # cd /etc/yum.repos.d/
    # mv rhel-source.repo rhel-source.repo.bak
    # wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
    # sed -i "s;\$releasever;6;g" CentOS6-Base-163.repo

#### 重新缓存包信息

    # yum clean all
    # yum makecache
    # yum repolist
    Loaded plugins: fastestmirror, product-id, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
    Determining fastest mirrors
    base                                                                            | 3.7 kB     00:00     
    base/primary_db                                                                 | 4.7 MB     00:00     
    extras                                                                          | 3.4 kB     00:00     
    extras/primary_db                                                               |  26 kB     00:00     
    updates                                                                         | 3.4 kB     00:00     
    updates/primary_db                                                              | 1.3 MB     00:00     
    repo id                               repo name                                                  status
    base                                  CentOS-6 - Base - 163.com                                  6,713
    extras                                CentOS-6 - Extras - 163.com                                   33
    updates                               CentOS-6 - Updates - 163.com                                 142
    repolist: 6,888

#### 愉快的yum install

    # yum install -y telnet lrzsz
    ......
    Installed:
      lrzsz.x86_64 0:0.12.20-27.1.el6                                                                           telnet.x86_64 1:0.17-48.el6       

---

#### 参考文档

* 备用下载链接： [红帽6重装yum所需的rpm包(Redhat_6.x_yum_solution).zip:](http://metastead.com/7SpB)