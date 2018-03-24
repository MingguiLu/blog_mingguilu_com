---
title: RAID阵列上的Server_2012_R2忘记Administrator密码后...
description: ''
photos: /images/20180302/20180302_10.png
date: 2018-03-02 15:27:52
tags:
- Windows Server
- RAID
categories: 运维之道
---

配置了RAID5阵列的Dell服务器丢失了Administrator密码，运行着Windows Server 2012 R2，WinPE没有加载RAID驱动也无能为力，最后使用Windows Server 2012 R2的安装光盘重置了密码

<!--more-->

#### 从安装光盘启动

![](/images/20180302/20180302_01.png)

#### 在安装程序中选择"修复计算机"

![](/images/20180302/20180302_02.png)

#### 选择选项"疑难解答"

![](/images/20180302/20180302_03.png)

#### 选择高级选项"命令提示符"

![](/images/20180302/20180302_04.png)

#### 将Windows\system32下的osk.exe程序替换成cmd.exe程序

        # 进入系统盘的Windows\system32目录下
        cd Windows\system32
        # 将osk.exe(屏幕键盘程序)重命名做备份
        rename osk.exe osk.exe.old
        # 拷贝cmd.exe程序并重命名为osk.exe
        copy cmd.exe osk.exe

![](/images/20180302/20180302_05.png)

#### 选择"继续"，重启服务器到登录界面

![](/images/20180302/20180302_06.png)

#### 点击"屏幕键盘"，弹出命令行终端

![](/images/20180302/20180302_07.png)

#### 用DOS命令重置Administrator密码，或创建新用户

        # 重置Administrator密码
        net user administrator  New_Password
        # 创建新帐户
        net user New_Username New_Password
        # 将新帐户添加到本地管理员组
        net localgroup administrators New_Username

![](/images/20180302/20180302_08.png)

#### 登录系统

![](/images/20180302/20180302_09.png)

