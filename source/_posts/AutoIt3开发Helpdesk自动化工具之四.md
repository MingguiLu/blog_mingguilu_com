---
date: 2017-04-25T23:14:48+08:00
description: ""
tags: 
- AutoIt
- AutoIt3
- Helpdesk
- 自动化
title: AutoIt3开发Helpdesk自动化工具之四:软件安装
categories: 技术分享
photos: /images/photos/autoit3.png
---

本章将结合示例，演示如何编写自动安装软件的脚本

为了便于访问软件安装程序，我将所需的软件放置在共享文件夹中，并设置共享和NTFS权限为everyone可读

<!--more-->

### 一、安装Adobe Flash Player

由于Adobe Flash Player的更新升级非常频繁，安装文件名称随着每次升级变更，所以将安装文件名称修改为“Adobe_flashplayer_installer.exe”，这样每当更新安装文件之后，无需修改脚本内容。

    #cs ----------------------------------------------------------------------------
    
     AutoIt Version: 3.3.14.2
     Author:        MingguiLu
     Script Function:
    	  Adobe Flash Player
    
    #ce ----------------------------------------------------------------------------
    
    ; Script Start - Add your code below here
    
    WinMinimizeAll()
    
    Global $administratorUserName = "administrator"
    Global $administratorPassword = "Root@1024"
    
![](/images/20170425/170425_01_01_01.jpg)
    
    If IsAdmin() Then
       Run("\\wh-filesrv-01\Software\Adobe\Adobe_flashplayer_installer.exe")
    Else
       ;;以管理员账户运行安装软件
       RunAs($administratorUserName,@ComputerName,$administratorPassword,0,"\\wh-filesrv-01\Software\Adobe\Adobe_flashplayer_installer.exe","")
    EndIf
    ;; “”表示当前活动窗口
    WinWaitActive("")
    ;;获取当前活动窗口的标题，存储到变量tempFlashTitle中
    Local $tempFlashTitle = WinGetTitle("")
    WinWaitActive($tempFlashTitle,"我已经阅读并同意")
    SLEEP(500)
    ;;勾选“我已经阅读并同意Flash Player 许可协议的条款。”
    ControlCommand($tempFlashTitle,"我已经阅读并同意","Button4","Check","")
    SLEEP(500)
    ;;点击“安装”
    ControlClick($tempFlashTitle,"我已经阅读并同意","Button2","left",1)
    
Adobe Flash Player安装程序和IE浏览器会冲突，导致安装过程中会报错，需要使用if条件判断语句处理报错窗口

![](/images/20170425/170425_01_01_02.jpg)
    
    WinWaitActive("")
    ;;获取当前活动窗口的文本，存储到变量tempFlashText中
    Local $tempFlashText = WinGetText("")
    ;;如果窗口文本中包含“下列发生冲突的应用程序”字段，则点击“确定”关闭窗口
    If StringInStr($tempFlashText,"下列发生冲突的应用程序") Then
       SLEEP(500)
       ControlClick($tempFlashTitle,"下列发生冲突的应用程序","Button1","left",1)
       
![](/images/20170425/170425_01_01_03.jpg)
       
       ;;弹出消息框提示用户，2秒后自动消失
       MsgBox(64,"提示","请关闭IE浏览器后再尝试运行安装程序！",2)
       
![](/images/20170425/170425_01_01_04.jpg)
       
    Else
       ;;等待安装完成
       WinWaitActive($tempFlashTitle,"Flash Player")
       SLEEP(500)
       ;;点击“确定”关闭窗口
       ControlClick($tempFlashTitle,"Flash Player","Button2","left",1)
       WinWaitClose($tempFlashTitle,"Flash Player")
    EndIf
    
    ; Script End
    

### 二、安装linphone

    #cs ----------------------------------------------------------------------------
    
     AutoIt Version: 3.3.14.2
     Author:        MingguiLu
     Script Function:
    	Linphone
    
    #ce ----------------------------------------------------------------------------
    
    ; Script Start - Add your code below here
    
    WinMinimizeAll()
    
    Global $administratorUserName = "administrator"
    Global $administratorPassword = "Root@1024"
    
    If IsAdmin() Then
       Run("\\wh-filesrv-01\Software\Linphone\Linphone-3.10.2-win32.exe")
    Else
       RunAs($administratorUserName,@ComputerName,$administratorPassword,0,"\\wh-filesrv-01\Software\Linphone\Linphone-3.10.2-win32.exe","")
    EndIf
    
![](/images/20170425/170425_01_02_01.jpg)
    
    WinWaitActive("")
    ;;获取当前活动窗口的文本，存储到变量tempLinphoneText中
    Local $tempLinphoneText = WinGetText("")
    ;;判断字符“installed”是否包含在当前活动窗口文本中，并将结果存储在变量tempResult中
    Local $tempResult = StringInStr($tempLinphoneText,"installed",0,1)
    ;;如果tempResult为True，表示Linphone已安装，可结束安装程序
    If $tempResult Then
       WinActivate("Linphone 3.10.2 安装","Linphone is already installed")
       WinWaitActive("Linphone 3.10.2 安装","Linphone is already installed")
       SLEEP(500)
       ;;点击“取消”
       ControlClick("Linphone 3.10.2 安装","Linphone is already installed","Button2","left",1)
       WinWaitClose("Linphone 3.10.2 安装","Linphone is already installed")
       
![](/images/20170425/170425_01_02_02.jpg)
       
    ;;如果tempResult为False，表示Linphone未安装，可继续完成安装
    Else
       WinActivate("Linphone 3.10.2 安装","欢迎使用“Linphone 3.10.2”安装向导")
       WinWaitActive("Linphone 3.10.2 安装","欢迎使用“Linphone 3.10.2”安装向导")
       SLEEP(500)
       ;;点击“下一步（N）”
       Send("!n")
       SLEEP(500)
       
![](/images/20170425/170425_01_02_03.jpg)
       
       WinWaitActive("Linphone 3.10.2 安装","许可证协议")
       SLEEP(500)
       ;;点击“我接受（I）”
       Send("!i")
       SLEEP(500)
       
![](/images/20170425/170425_01_02_04.jpg)
       
       WinWaitActive("Linphone 3.10.2 安装","选择安装位置")
       SLEEP(500)
       ;;点击“下一步（N）”
       Send("!n")
       SLEEP(500)
       
![](/images/20170425/170425_01_02_05.jpg)
       
       WinWaitActive("Linphone 3.10.2 安装",'选择“开始菜单”文件夹')
       SLEEP(500)
       ;;点击“下一步（N）”
       Send("!n")
       SLEEP(500)
       
![](/images/20170425/170425_01_02_06.jpg)
       
       WinWaitActive("Linphone 3.10.2 安装","选择组件")
       SLEEP(500)
       ;;按下方向键下
       Send("{DOWN}")
       SLEEP(500)
       ;;按下空格键，选中“Cisco'S OpenH264 Codec”
       Send("{SPACE}")
       SLEEP(500)
       ;;点击“安装（I）”
       Send("!i")
       
![](/images/20170425/170425_01_02_07.jpg)
       
       WinWaitActive("Linphone 3.10.2 安装",'正在完成“Linphone 3.10.2”安装向导')
       SLEEP(1000)
       ;;点击“完成（F）”
       Send("!f")
       WinWaitClose("Linphone 3.10.2 安装",'正在完成“Linphone 3.10.2”安装向导')
    EndIf
    
    ; Script End

### 三、安装Cisco VPN Client

    #cs ----------------------------------------------------------------------------
    
     AutoIt Version: 3.3.14.2
     Author:         MingguiLu
    
     Script Function:
    	  Cisco VPN Client
    
    
    #ce ----------------------------------------------------------------------------
    
    ; Script Start - Add your code below here
    
    ;;最小化所有窗口
    WinMinimizeAll()
    
    Global $administratorUserName = "administrator"
    Global $administratorPassword = "Root@1024"
    
    If IsAdmin() Then
       Run("\\wh-filesrv-01\Software\Cisco\Cisco.VPN.Client.v5.0.07.0290.x64\setup.exe")
    Else
       RunAs($administratorUserName,@ComputerName,$administratorPassword,0,"\\wh-filesrv-01\Software\Cisco\Cisco.VPN.Client.v5.0.07.0290.x64\setup.exe","")
    EndIf
    
![](/images/20170425/170425_01_03_01.jpg)
    
    WinWaitActive("WinZip Self-Extractor - setup.exe","To unzip all files in setup.exe")
    SLEEP(500)
    ControlClick("WinZip Self-Extractor - setup.exe","To unzip all files in setup.exe","Button4","left",1)
    
![](/images/20170425/170425_01_03_02.jpg)
    
    WinWaitActive("WinZip Self-Extractor","确定")
    SLEEP(500)
    ControlClick("WinZip Self-Extractor","确定","Button1","left",1)
    
![](/images/20170425/170425_01_03_03.jpg)
    
    WinWaitActive("Cisco Systems VPN Client 5.0.07.0290","This installation can be displayed in multiple languages")
    SLEEP(500)
    ControlClick("Cisco Systems VPN Client 5.0.07.0290","This installation can be displayed in multiple languages","Button1","left",1)
    SLEEP(1000)
    
如果已安装过Cisco VPN Client软件，再重复安装，将会有下图所示的报错窗口，需要使用if条件判断语句处理报错窗口

![](/images/20170425/170425_01_03_09.jpg)
    
    ;;如果存在标题为“Installer informaton”,文本内容包含"Error 28000:"的活动窗口，说明该软件已安装，可结束安装程序
    If WinExists("Installer Information","Error 28000:") Then
       WinActivate("Installer Information","Error 28000: Before installing the Cisco Systems VPN Client 5.0.07.0290")
       WinWaitActive("Installer Information","Error 28000: Before installing the Cisco Systems VPN Client 5.0.07.0290")
       SLEEP(500)
       ControlClick("Installer Information","Error 28000: Before installing the Cisco Systems VPN Client 5.0.07.0290","Button1","left",1)
    
![](/images/20170425/170425_01_03_10.jpg)
    
       WinWaitActive("Fatal Error","Installation ended prematurely because of an error")
       SLEEP(500)
       ControlClick("Fatal Error","Installation ended prematurely because of an error","Button1","left",1)
       WinWaitClose("Fatal Error","Installation ended prematurely because of an error")
    
![](/images/20170425/170425_01_03_04.jpg)
    
    Else
       WinWaitActive("Cisco Systems VPN Client 5.0.07.0290 Setup","Welcome to the Cisco Systems VPN Client")
       SLEEP(500)
       Send("!n")
    
![](/images/20170425/170425_01_03_05.jpg)
    
       WinWaitActive("Cisco Systems VPN Client 5.0.07.0290 Setup","License Agreemen")
       SLEEP(500)
       Send("!a")
       SLEEP(500)
       Send("!n")
    
![](/images/20170425/170425_01_03_06.jpg)
    
       WinWaitActive("Cisco Systems VPN Client 5.0.07.0290 Setup","Destination Folder")
       SLEEP(500)
       Send("!n")
    
![](/images/20170425/170425_01_03_07.jpg)
    
       WinWaitActive("Cisco Systems VPN Client 5.0.07.0290 Setup","Ready to Install the Application")
       SLEEP(500)
       Send("!n")
       SLEEP(500)
    
![](/images/20170425/170425_01_03_08.jpg)
    
       WinWaitActive("Cisco Systems VPN Client 5.0.07.0290 Setup","successfully installed")
       ;;将vpn客户端配置文件拷贝到“C:\Program Files (x86)\Cisco Systems\VPN Client\Profiles”下
       FileCopy("\\wh-mingguilu-01\Installers\Cisco\Cisco.VPN.Client.v5.0.07.0290.x64\quark-IDC.pcf","C:\Program Files (x86)\Cisco Systems\VPN Client\Profiles",1)
       SLEEP(500)
       ;;点击“Finish”关闭窗口
       ControlClick("Cisco Systems VPN Client 5.0.07.0290 Setup","successfully installed","Button1","left",1)
       ;;点击“Finish”关闭窗口可能弹出标题为“Installer Information”的提示框口，也可能没有，需要使用if条件判断语句处理报错窗口
       If WinExists("Installer Information") Then
    	  WinActivate("Installer Information","You must restart your system")
    	  WinWaitActive("Installer Information","You must restart your system")
    	  SLEEP(500)
    	  ControlClick("Installer Information","You must restart your system","Button2","left",1)
    	  WinWaitClose("Installer Information","You must restart your system")
       Else
    	  WinWaitClose("Cisco Systems VPN Client 5.0.07.0290 Setup","successfully installed")
       EndIf
    EndIf
    
    ; Script End
