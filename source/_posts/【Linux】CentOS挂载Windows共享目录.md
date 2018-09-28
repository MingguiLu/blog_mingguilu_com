---
title: 【Linux】CentOS挂载Windows共享目录
tags:
  - linux
  - centos
  - mount
categories: 点滴随笔
photos: 
date: 2018-09-28 22:08:57
description: 
---

#### 网络互通

先保证Linux和Windows网络互通，嗯，这句是废话！

#### Windows设置共享目录

![](/images/20180928/20180928_01.png)

#### 给拥有共享目录访问权限的Windows用户设置密码

![](/images/20180928/20180928_02.png)

<!--more-->

#### 在CentOS上挂在Windows共享目录

    # mount -t cifs -o username=administrator //192.168.1.202/d /mnt
    mount: //192.168.1.202/d is write-protected, mounting read-only
    mount: cannot mount //192.168.1.202/d read-only

挂载失败，如显示上面的报错，需要安装cifs

#### 安装cifs

注意安装时cifs接星号*

    # yum install -y cifs*
    ......
    Installed:
      cifs-utils.x86_64 0:6.2-10.el7                 cifs-utils-devel.x86_64 0:6.2-10.el7                

    Dependency Installed:
      avahi-libs.x86_64 0:0.6.31-19.el7                    cups-libs.x86_64 1:1.6.3-35.el7                
      keyutils.x86_64 0:1.5.8-3.el7                        libldb.x86_64 0:1.2.2-1.el7                    
      libtalloc.x86_64 0:2.1.10-1.el7                      libtdb.x86_64 0:1.3.15-1.el7                   
      libtevent.x86_64 0:0.9.33-2.el7                      libwbclient.x86_64 0:4.7.1-9.el7_5             
      samba-client-libs.x86_64 0:4.7.1-9.el7_5             samba-common.noarch 0:4.7.1-9.el7_5            
      samba-common-libs.x86_64 0:4.7.1-9.el7_5            

    Complete!

##### 再次尝试挂载

    # mount -t cifs -o username=administrator //192.168.1.202/d /mnt
    Password for administrator@//192.168.1.202/d:  *******
    
    # df -h
    Filesystem               Size  Used Avail Use% Mounted on
    /dev/mapper/centos-root   17G  1.3G   16G   8% /
    devtmpfs                 476M     0  476M   0% /dev
    tmpfs                    488M   12K  488M   1% /dev/shm
    tmpfs                    488M  7.7M  480M   2% /run
    tmpfs                    488M     0  488M   0% /sys/fs/cgroup
    /dev/sda1               1014M  130M  885M  13% /boot
    tmpfs                     98M     0   98M   0% /run/user/1000
    tmpfs                     98M     0   98M   0% /run/user/0
    //192.168.1.202/d        401G  188G  213G  47% /mnt

挂载成功！

