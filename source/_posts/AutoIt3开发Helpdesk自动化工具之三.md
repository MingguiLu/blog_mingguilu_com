---
date: 2017-04-23T14:55:43+08:00
description: ""
tags: 
- AutoIt
- AutoIt3
- Helpdesk
- 自动化
title: AutoIt3开发Helpdesk自动化工具之三:系统配置
categories: 运维之道
photos: /images/photos/autoit3.png
---

本章将结合示例，演示如何编写自动配置系统的脚本

<!--more-->

### 一、修改administrator密码

    #cs ----------------------------------------------------------------------------
    
     AutoIt Version: 3.3.14.2
     Author:         MingguiLu
    
     Script Function:
    	修改Administrator密码.
    
    #ce ----------------------------------------------------------------------------
    
    ; Script Start - Add your code below here
    
    ;;最小化所有窗口
    WinMinimizeAll()
    
    ;;定义变量用于存储administrator的密码
    Global $administratorPassword = "Root@1024"
    
    ;;如果当前为管理员即可重设密码，如果为普通用户弹出警告提示
    If IsAdmin() Then
       Run("net user administrator " & $administratorPassword)
       SLEEP(500)
    Else
       MsgBox(64,"警告","当前用户无权操作administrator密码，请切换其它管理员帐户操作！")
       SLEEP(500)
    EndIf
    
    ; Script End


### 二、创建普通用户Admin

当电脑在外网或脱域、administrator密码被篡改或不便告知用户、用户忘记了域账户的登录密码等极端情况下，无法登录到桌面时，可使用admin用户登录到桌面后运行远程协助软件

![](/images/20170423/170423_01_02_01.jpg)
    
     #cs ----------------------------------------------------------------------------
    
     AutoIt Version: 3.3.14.2
     Author:         MingguiLu
    
     Script Function:
        创建普通用户admin
    
    #ce ----------------------------------------------------------------------------
    
    ; Script Start - Add your code below here
    
    ;;最小化所有窗口
    WinMinimizeAll()
    
    If IsAdmin() Then
       ;;创建用户admin,密码设置为:abc@123
       Run("net user admin abc@123 /add")
       ;;将用户admin添加到本地组users中
       Run("net localgroup users admin /add")
       ;;打开计算机管理控制台
       ShellExecute(@SystemDir & "\compmgmt.msc")
       WinWaitActive("计算机管理","计算机管理(本地)")
       ;;暂停脚本半秒钟
       SLEEP(500)
       ;;按下方向键下键
       Send("{DOWN}",5)
       SLEEP(500)
       Send("{DOWN}")
       SLEEP(500)
       Send("{DOWN}")
       SLEEP(500)
       Send("{DOWN}")
       SLEEP(500)
       Send("{DOWN}")
       SLEEP(500)
       ;;按下方向键右键
       Send("{RIGHT}")
       SLEEP(500)
       Send("{DOWN}")
       SLEEP(500)
       ;;按下Tab键
       Send("{TAB}")
       SLEEP(500)
       ;;按下回车键
       Send("{ENTER}")
       WinWaitActive("admin 属性","常规")
       SLEEP(500)
       ;;选中“用户不能更改密码”
       ControlCommand("admin 属性","常规","Button2","Check","")
       SLEEP(500)
       ;;选中“密码永不过期”
       ControlCommand("admin 属性","常规","Button3","Check","")
       SLEEP(500)
       ;;点击“确定”
       ControlClick("admin 属性","常规","Button6","left",1)
       WinActivate("计算机管理","计算机管理(本地)")
       WinWaitActive("计算机管理","计算机管理(本地)")
       SLEEP(500)
       WinClose("计算机管理","计算机管理(本地)")
       WinWaitClose("计算机管理","计算机管理(本地)")
    Else
       MsgBox(64,"警告","当前用户无权操作用户和组，请切换其它管理员帐户操作！")
    EndIf
    
    ; Script End   


### 三、修改计算机名并加域

当开始运行脚本时，弹出一个交互窗口用于输入计算机名，并存储到变量中以便之后调用。

    #cs ----------------------------------------------------------------------------
    
     AutoIt Version: 3.3.14.2
     Author:         MingguiLu
    
     Script Function:
    	修改计算机名并加域.
    
    #ce ----------------------------------------------------------------------------
    
    ; Script Start - Add your code below here
    
    ;;最小化所有窗口
    WinMinimizeAll()
    
    Global $administratorUserName = "administrator"
    Global $administratorPassword = "Root@1024"
    
    ;;定义变量$domainName存储域名
    Global $domainName = "awesomeit.com"
    ;;定义变量$itUserName存储IT的账户
    Global $itUserName = "mingguilu"
    ;;定义变量$itPassword存储IT的密码
    Global $itPassword = "Password@1024"

![](/images/20170423/170423_01_03_01.jpg)
    
    ;;定义变量$hostName存储用户交互中输入的计算机名
    Global $hostName
    Global $hostName = InputBox("输入","请输入计算机名：","")

![](/images/20170423/170423_01_03_02.jpg)
    
    If IsAdmin() Then
       ;;打开系统属性控制台
       Run("control sysdm.cpl")
    Else
       ;;以管理员模式打开系统属性控制台
       RunAs($administratorUserName,@ComputerName,$administratorPassword,0,"control sysdm.cpl")
    EndIf
    WinWaitActive("系统属性","计算机名")
    SLEEP(500)
    ;;进入“更改”
    Send("!c")
    
![](/images/20170423/170423_01_03_03.jpg)  
    
    WinWaitActive("计算机名/域更改","计算机名(&C):")
    SLEEP(500)
    WinActivate("计算机名/域更改","计算机名(&C):")
    ;;自动填写计算机名
    ControlSetText("计算机名/域更改","计算机名(&C):","Edit1",$hostName)
    SLEEP(500)
    ;;选中“域（D）”
    ControlCommand("计算机名/域更改","计算机名(&C):","Button3","Check")
    SLEEP(500)
    ;;自动填写域名
    ControlSetText("计算机名/域更改","计算机名(&C):","Edit3",$domainName)
    SLEEP(500)
    ;;点击“确定”
    ControlClick("计算机名/域更改","计算机名(&C):","Button6","left",1)
    
![](/images/20170423/170423_01_03_04.jpg)  
    
    WinWaitActive("Windows 安全")
    SLEEP(500)
    ;;自动填写IT人员账号
    ControlSetText("Windows 安全","","Edit1",$itUserName)
    SLEEP(500)
    ;;自动填写IT人员密码
    ControlSetText("Windows 安全","","Edit2",$itPassword)
    SLEEP(500)
    ;;点击“确定”
    ControlClick("Windows 安全","","Button2","left",1)
    
![](/images/20170423/170423_01_03_05.jpg)  

    WinWaitActive("计算机名/域更改","欢迎加入 awesomeit.com 域")
    SLEEP(500)
    ControlClick("计算机名/域更改","欢迎加入 awesomeit.com 域","Button1","left",1) 
     
为了兼容可能弹出的提示框，使用while循环结合if条件判断，做应对的操作：如果8秒钟之内弹出“帐户名与安全标识间无任何映射完成”的提示框，则按下回车键关闭提示框并跳出循环；如果没有弹出这个提示框，则8秒之后结束循环
     
    Local $i = 0
        While $i <= 8000
           If WinExists("计算机名/域更改","帐户名与安全标识间无任何映射完成") Then
            	  WinActivate("计算机名/域更改","帐户名与安全标识间无任何映射完成")
            	  WinWaitActive("计算机名/域更改","帐户名与安全标识间无任何映射完成")
            	  SLEEP(500)
            	  Send("{ENTER}")
            	  ExitLoop
           Else
            	  SLEEP(1000)
            	  $i = $i + 1000
           EndIf
    WEnd
    
![](/images/20170423/170423_01_03_06.jpg) 
    
    WinWaitActive("计算机名/域更改","确定")
    SLEEP(500)
    Send("{ENTER}")
    
    WinWaitActive("系统属性","计算机名")
    SLEEP(500)
    ControlClick("系统属性","计算机名","Button3","left",1)
    
    WinWaitActive("Microsoft Windows")
    SLEEP(500)
    Send("!l")
    WinWaitClose("Microsoft Windows")
    
    ; Script End
 