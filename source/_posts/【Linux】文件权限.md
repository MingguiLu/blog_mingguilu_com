---
title: 【Linux】文件权限
tags:
  - linux
  - 文件权限
categories: 点滴随笔
photos: /images/photos/linux.jpg
date: 2018-08-21 12:05:35
description:
---

Linux一切皆文件，但每个文件类型不尽相同

| 字符标示 | 文件类型 |
| :---: | :---: |
| - | 普通文件 |
| d | 目录文件 |
| l | 链接文件 |
| b | 块设备文件 |
| c | 字符设备文件 |
| p | 管道文件 |

<!--more-->

#### 一般权限

##### 文件权限的字符和数字标示

| 权限项 | 读 | 写 | 执行 |
| :---: | :---: | :---: |
| 字符标示 | r | w | x |
| 数字标示 | 4 | 2 | 1 |
| 解析(文件) | 读取文件内容 | 编辑、新增、修改、删除文件内容 | 能够运行一个脚本程序 |
| 解析(目录) | 读取目录内的文件列表 | 目录内新增、删除、重命名文件 | 能够进入目录 | 

##### chmod 设置文件或目录的权限 

语法： chmod [参数] 权限 文件或目录名称

##### chown 设置文件或目录的所有者和所属组 
 
语法： chown [参数] 所有者:所属组 文件和目录名称

chmod和chown都可以使用参数 -R 递归操作，即对目录内所有的文件进行整体操作

#### 特殊权限

##### SUID 

SUID权限位是一种对二进制程序进行设置的特殊权限，可以让二进制程序的执行者临时拥有属主的全选（仅对拥有执行权限的二进制程序有效）

| 原所有者权限 | 添加SUID权限 | 变更后的权限  |
| :---: | :---: | :---: |
| 原本有x执行权限 | chmod u+s | x执行权限位替换成s (小写s) |
| 原本无x执行权限 | chmod u+s | x执行权限位替换成S (大写S) |

以/bin/passwd为例，passwd命令修改用户密码，而用户密码保存在/etc/shadow文件中，shadow文件默认权限000，仅root有读取和修改的权限，给/bin/passwd赋予SUID权限后，普通用户执行passwd也可以修改/etc/shadow文件，修改自己的密码

![](/images/20180821/20180821_01.jpg)

搜索系统中包含SUID权限的所有文件： 

    # find / -perm -4000 
    find: '/proc/51211/task/51211/fd/6': No such file or directory
    find: '/proc/51211/task/51211/fdinfo/6': No such file or directory
    find: '/proc/51211/fd/6': No such file or directory
    find: '/proc/51211/fdinfo/6': No such file or directory
    /usr/bin/chfn
    /usr/bin/chsh
    /usr/bin/chage
    /usr/bin/gpasswd
    /usr/bin/newgrp
    /usr/bin/mount
    /usr/bin/su
    /usr/bin/umount
    /usr/bin/at
    /usr/bin/sudo
    /usr/bin/pkexec
    /usr/bin/crontab
    /usr/bin/passwd
    /usr/sbin/pam_timestamp_check
    /usr/sbin/unix_chkpwd
    /usr/lib/polkit-1/polkit-agent-helper-1
    /usr/lib64/dbus-1/dbus-daemon-launch-helper


##### SGID

SGID权限位有2个功能，一是让执行者临时拥有属组的权限(对拥有执行权限的二进制程序进行设置)；二是在某个目录中创建的文件自动继承该目录的用户组(只可以对目录进行设置)

| 原所属组权限 | 添加SGID权限 | 变更后的权限  |
| :---: | :---: | :---: |
| 原本有x执行权限 | chmod g+s | x执行权限位替换成s (小写s) |
| 原本无x执行权限 | chmod g+s | x执行权限位替换成S (大写S) |

以创建/tmp/test_SGID目录为例，设置test_SGID目录权限为777，这样所有用户都可以在其下创建文件和目录，并且所创建的文件和目录的所有者和所属组是用户本身；但给test_SGID目录添加SGID权限后，用户在其中所创建的文件和目录都会继承该目录的用户组，而不是用户自己所属组

![](/images/20180821/20180821_02.jpg)

搜索系统中包含SGID权限的所有文件： 

    # find / -perm -2000
    find: '/proc/51231/task/51231/fd/6': No such file or directory
    find: '/proc/51231/task/51231/fdinfo/6': No such file or directory
    find: '/proc/51231/fd/6': No such file or directory
    find: '/proc/51231/fdinfo/6': No such file or directory
    /run/log/journal
    /run/log/journal/bf60cc67e1734d16b3a1dd9e8b138760
    /tmp/test_SGID
    /usr/bin/wall
    /usr/bin/write
    /usr/bin/ssh-agent
    /usr/sbin/postdrop
    /usr/sbin/postqueue
    /usr/libexec/utempter/utempter
    /usr/libexec/openssh/ssh-keysign

##### SBIT

SBIT权限位可以确保用户只能删除自己的文件，而不能删除其他用户的文件，即文件和目录只能被其所有者执行删除操作

| 原其他人权限 | 添加SBIT权限 | 变更后的权限  |
| :---: | :---: | :---: |
| 原本有x执行权限 | chmod o+t | x执行权限位替换成t (小写t) |
| 原本无x执行权限 | chmod o+t | x执行权限位替换成T (大写T) |

以/tmp目录为例，/tmp目录默认已经设置了SBIT权限，用itadmin账户创建一个文件，并把文件权限修改为777，切换另外一个账户testuser，及时拥有rwx权限，testuser用户依然无法删除itadmin创建的文件

![](/images/20180821/20180821_03.jpg)

#### 隐藏权限

##### chattr 添加/删除隐藏权限

语法： chattr [参数] 文件

+参数 添加某个隐藏权限到文件
-参数 把文件的某个隐藏权限移除

| 参数 | 作用 |
| :---: | :---: |
| i | 无法对文件进行修改；对目录设置后，仅能修改其中的子文件内容而不能新建或删除文件 |
| a | 仅允许追加内容，无法覆盖/删除内容(Append Only) |
| A | 不再修改文件或目录的最后访问时间(atime) |
| s | 彻底从硬盘中删除，不可恢复(用0填充原文件所在硬盘区域) |
| S | 文件内容在变更后立即同步到硬盘(sync) |
| b | 不再修改文件或目录的存取时间 |
| d | 使用dump命令备份时忽略本文件/目录 |
| D | 检查压缩文件中的错误 |
| c | 默认将文件或目录进行压缩 |
| u | 当删除该文件后依然保留其在硬盘中的数据，方便日后恢复 |
| t | 让文件系统支持尾部合并(tail-merging) |
| X | 可以直接访问压缩文件中的内容 |

##### lsattr 显示隐藏权限

语法：　lsattr [参数] 文件

![](/images/20180821/20180821_04.jpg)

#### 文件访问控制列表(ACL)

ACL针对指定的用户和用户组设置文件或目录的操作权限；对目录设置ACL，则目录中的文件会继承其ACL；对文件设置ACL，则文件不再继承其所在目录的ACL

##### setfacl　设置ACL的规则

语法：　setfacl [参数] 文件

| 参数 | 作用 |
| :---: | :---: |
| -R | 针对目录递归操作 |
| -m | 针对普通文件 |
| -b | 删除ACL |

对目录必须加参数　-Rm
对文件必须加参数　-m

使用ll或者ls -l命令看到文件权限最后一个点(.)变成加号(+)，就意味着该文件已经设置了ACL

##### getfacl 显示ACL权限

语法：　getfacl 文件

![](/images/20180821/20180821_05.jpg)


