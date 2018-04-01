---
date: 2017-04-27T15:05:10+08:00
description: ""
tags: 
- AutoIt
- AutoIt3
- Helpdesk
- 自动化
title: AutoIt3开发Helpdesk自动化工具之五:多任务组合
categories: 运维之道
photos: /images/photos/autoit3.png
---

前两篇示例中，分别创建了系统配置和软件安装的3个脚本，通过“Compile Script to .exe (x86/x64)”将.au3脚本转换为.exe可执行程序，之后运行对应程序即可进行自动安装配置。

    ├── 修改administrator密码.au3
    ├── 创建admin用户.au3
    ├── 修改计算机名并加域.au3
    ├── Cisco VPN  Client.au3
    ├── Adobe Flash.au3
    └── Linphone.au3

但是单独使用某一个脚本的场景并不多，通常我们根据不同需要自动完成多个任务，例如

呼叫中心的电话销售：

修改administrator密码 + 修改计算机名并加域 + Adobe Flash + Linphone

各城市门店的业务员：

修改administrator密码 + 创建admin用户 + 修改计算机名并加域 + Adobe Flash + Cisco VPN Client

<!--more-->

#### 可以通过AutoIt3的用户自定义函数实现多个任务的组合

* 将每个功能脚本定义为一个函数
* 将所有的自定义函数放在同一个脚本
* 通过依次调用多个函数实现多个自动化任务

下面看一下脚本模型

    #cs ----------------------------------------------------------------------------
    
     AutoIt Version: 3.3.14.2
     Author:         MingguiLu
    
     Script Function:
    	Helpdesk自动化配置脚本.
    
    #ce ----------------------------------------------------------------------------
    
    ; Script Start - Add your code below here
    
    _changePassword()	;;调用函数 _changePassword()，即执行“修改administrator密码”的脚本
    ;_createAdmin()  ;;添加了注释符号，不会调用函数 _createAdmin()，即不执行“创建admin用户”的脚本
    _joinDomain()   ;;调用函数 _joinDomain()，即执行“修改计算机名并加域”的脚本
    _adobeFlash()   ;;调用函数 _adobeFlash()，即执行“Adobe Flash”的脚本
    ;_linPhone()    ;;添加了注释符号，不会调用函数 _linPhone()，即不执行 “Linphone”的脚本
    _ciscoVpnClient()   ;;调用函数 _ciscoVpnClient()，即执行“Cisco VPN Client”的脚本
    
    
    Func _changePassword()  ;;创建一个自定义函数 _changePassword()
    
       ;;Script	“修改administrator密码”的脚本
    
    EndFunc
    
    
    Func _createAdmin()
    
       ;;Script “创建admin用户”的脚本
    
    EndFunc
    
    Func _joinDomain()
    
       ;;Script “修改计算机名并加域”的脚本
    
    EndFunc
    
    Func _adobeFlash()
    
       ;;Script “Adobe Flash”的脚本
    
    EndFunc
    
    Func _linPhone()
    
       ;;Script “Linphone”的脚本
    
    EndFunc
    
    Func _ciscoVpnClient()
    
       ;;Script “Cisco VPN Client”的脚本
    
    EndFunc
    
    ; Script End
    

使用以上的脚本模型，我们可以持续维护一份脚本，优化现有的函数或者添加新的函数，需要执行不同任务组合时，拷贝一份脚本并调用相应的函数即可。

