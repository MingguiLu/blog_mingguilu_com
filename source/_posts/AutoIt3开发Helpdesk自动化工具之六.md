---
date: 2017-05-02T19:05:10+08:00
description: ""
tags: 
- AutoIt
- AutoIt3
- Helpdesk
- 自动化
title: AutoIt3开发Helpdesk自动化工具之六:图形用户界面
categories: 技术分享
photos: /images/photos/autoit3.png
---

在持续的编写和测试AutoIt3脚本过程中，对AutoIt的理解更深入了，我意识到可以使用AutoIt3 GUI编写一个图形用户界面，从而增强对每个自定义函数的复用，最终大幅提高运行效率。

### 参考文档

* AutoIt3安装目录中的官方英文手册 v3.3.14.2 - GUI相关 ： AutoIt.chm  
* AutoIt 在线手册中文版_脚本之家 v3.1.1 - GUI相关 :  http://www.jb51.net/shouce/autoit/ 

### GUI的概念

	AutoIt 脚本可创建（由窗口及其控件组成的）简单的图形用户界面（GUI）
	
	GUI 可由一个或多个 窗口 组成，这些窗口又带有一个或多个控件。GUI是靠“事件驱动”实现与用户的交互的，比如像点击按钮这样的动作就会引发一个事件。程序在空闲状态时等待事件的发生，捕捉到事件时则根据事件执行相关操作。


用GUI创建的图形用户界面确实很简单，类似的xp或更早风格的窗口，或许有方法可以美化，但是好看显然不是我关注的地方，我希望它稳定可靠地执行脚本就够了。GUI创建的窗口就如同每个我们所操作的软件操作，有文字，有按钮，可鼠标点击，可键盘输入......

<!--more-->

### GUI事件模式

* 消息循环模式（默认）

* OnEvent（事件驱动） 模式

这两种模式我以地铁站为例来理解，`消息循环模式`就像地铁站外临街出入口，在运营时间内无论是否有人进出每时每刻都开着；`OnEvent(事件驱动)模式`就像地铁站内的进出闸门，只有当乘客刷卡时才会打开，行人通过后，又回到关闭等待状态；

所以这两种模式各有适用的场景，自动化helpdesk工具可以在GUI上进行键鼠操作来命令脚本开始执行，更适合用`OnEvent(事件驱动)模式`。

### 为Helpdesk自动化工具创建GUI

#### 在脚本开头调用 GUIConstants.au3文件

	;;包含了在编写GUI程序时要用到的所有变量和常量 
	#include <GUIConstants.au3>

	;;包含了在编写GUI程序时使用复选框所需的变量和常量
	#include <GuiButton.au3>
	

#### 将默认的 消息循环模式 切换为  OnEvent(事件驱动) 模式

	Opt("GUIOnEventMode", 1)

#### 创建窗口和控件

为了方便后期的维护，可以将这部分脚本包含到一个自定义函数中，并在脚本开头适当位置调用
	
	_mainWindow()
	
	;;窗口创建后是处于隐藏状态的，必须使用本函数来显示
	GUISetState()
	
	Func _mainWindow()
	
	   ;;声明变量 $Checkbox[]
	   Global $Checkbox[53]
	
	   ;;AutoIt GUI的控件定位方式类似于前端CSS中的固定定位，即所有控件定位都以窗口左上角为原点
	
	   ;;将窗口的长度和宽度定义为变量
	   Global $gui_width = 490
	   Global $gui_height = 500
	
	   ;;将控件左侧和上方的定位参数定义为变量，当后期需要调整窗口大小和控件位置时，修改相应的变量值即可
	   Global $checkbox_left = 5
	   Global $sysconf_top = 10
	   Global $install_top = 155
	   Global $user_top = 375
	   Global $checkbox_width = 150
	   Global $checkbox_height = 20
	
	   Global $select_left = 5
	   Global $select_top = 300
	   Global $select_width = 50
	   Global $select_height = 30
	
	   ;;创建一个窗口，语法：GUICreate ( "窗口标题" [, 宽度 [, 高度 [, 左侧 [, 上方 [, 风格 [, 扩展风格 [, 父窗口]]]]]]] )
	   GUICreate("自动化配置工具 V0.5  - By MingguiLu", $gui_width, $gui_height)
	
	   ;;创建一个组框，语法：GUICtrlCreateGroup ( "文本", 左侧, 上方 [, 宽度 [, 高度 [, 风格 [, 扩展风格]]]] )
	   GUICtrlCreateGroup("系统配置", $checkbox_left, $sysconf_top, $gui_width-10, $gui_height/3-30)
	   ;;创建一个复选框，语法：GUICtrlCreateCheckbox ( "文本", 左侧, 上方 [, 宽度 [, 高度 [, 风格 [, 扩展风格]]]] )
	   $Checkbox[1] = GUICtrlCreateCheckbox("修改计算机名并加域", $checkbox_left+5, $sysconf_top+20, $checkbox_width, $checkbox_height)
	   $Checkbox[2] = GUICtrlCreateCheckbox("修改管理员密码", $checkbox_left+160, $sysconf_top+20, $checkbox_width, $checkbox_height)
	   $Checkbox[3] = GUICtrlCreateCheckbox("创建Admin用户", $checkbox_left+320, $sysconf_top+20, $checkbox_width, $checkbox_height)
	   $Checkbox[4] = GUICtrlCreateCheckbox("添加IT服务台", $checkbox_left+5, $sysconf_top+50, $checkbox_width, $checkbox_height)
	   $Checkbox[5] = GUICtrlCreateCheckbox("开启远程桌面", $checkbox_left+160, $sysconf_top+50, $checkbox_width, $checkbox_height)
	   $Checkbox[6] = GUICtrlCreateCheckbox("安装AD证书", $checkbox_left+320, $sysconf_top+50, $checkbox_width, $checkbox_height)
	   $Checkbox[7] = GUICtrlCreateCheckbox("关闭防火墙", $checkbox_left+5, $sysconf_top+80, $checkbox_width, $checkbox_height)
	   $Checkbox[8] = GUICtrlCreateCheckbox("降低UAC等级", $checkbox_left+160, $sysconf_top+80, $checkbox_width, $checkbox_height)
	
	   GUICtrlCreateGroup("软件安装", $checkbox_left, $install_top, $gui_width-10, $gui_height/3-30)
	   $Checkbox[21] = GUICtrlCreateCheckbox("Adobe Flash Player", $checkbox_left+5, $install_top+20, $checkbox_width, $checkbox_height)
	   $Checkbox[22] = GUICtrlCreateCheckbox("Google Chrome", $checkbox_left+160, $install_top+20, $checkbox_width, $checkbox_height)
	   $Checkbox[23] = GUICtrlCreateCheckbox("LinPhone for Windows", $checkbox_left+320, $install_top+20, $checkbox_width, $checkbox_height)
	   $Checkbox[24] = GUICtrlCreateCheckbox("Cisco VPN Client", $checkbox_left+5, $install_top+50, $checkbox_width, $checkbox_height)
	   $Checkbox[25] = GUICtrlCreateCheckbox("CRM Pro", $checkbox_left+160, $install_top+50, $checkbox_width, $checkbox_height)
	   $Checkbox[26] = GUICtrlCreateCheckbox("Avaya one-X", $checkbox_left+320, $install_top+50, $checkbox_width, $checkbox_height)
	   $Checkbox[27] = GUICtrlCreateCheckbox("Teamviewer11to10", $checkbox_left+5, $install_top+80, $checkbox_width, $checkbox_height)
	   $Checkbox[28] = GUICtrlCreateCheckbox("Teamviewer10", $checkbox_left+160, $install_top+80, $checkbox_width, $checkbox_height)
	
	   GUICtrlCreateGroup("操作选项",$select_left, $select_top, $gui_width-10, $gui_height/3-100)
	   ;;创建一个单选框，语法：GUICtrlCreateRadio ( "文本", 左侧, 上方 [, 宽度 [, 高度 [, 风格 [, 扩展风格]]]] )
	   Global $Radio1 = GUICtrlCreateRadio("驻地", $select_left+5, $select_top+20, $select_width, $select_height)
	   Global $Radio2 = GUICtrlCreateRadio("驻地(含VPN)", $select_left+55, $select_top+20, $select_width+40, $select_height)
	   Global $Radio3 = GUICtrlCreateRadio("销售", $select_left+150, $select_top+20, $select_width, $select_height)
	   Global $Radio4 = GUICtrlCreateRadio("客服", $select_left+200, $select_top+20, $select_width, $select_height)
	
	   GUICtrlCreateGroup("用户选项",$select_left, $user_top, $gui_width-10, $gui_height/3-100)
	   $Checkbox[43] = GUICtrlCreateCheckbox("驻地", $checkbox_left+5, $user_top+20, $checkbox_width-100, $checkbox_height+10)
	   $Checkbox[44] = GUICtrlCreateCheckbox("销售", $checkbox_left+65, $user_top+20, $checkbox_width-100, $checkbox_height+10)
	   $Checkbox[45] = GUICtrlCreateCheckbox("客服", $checkbox_left+135, $user_top+20, $checkbox_width-100, $checkbox_height+10)
	   $Checkbox[41] = GUICtrlCreateCheckbox("取消自动登录", $checkbox_left+205, $user_top+20, $checkbox_width-50, $checkbox_height+10)
	   $Checkbox[42] = GUICtrlCreateCheckbox("取消管理员权限", $checkbox_left+305, $user_top+20, $checkbox_width-50, $checkbox_height+10)
	
	   $Checkbox[51] = GUICtrlCreateCheckbox("自动重启系统+登录账户", $select_left+90, $user_top+85, $select_width+100, $select_height)
	   Global $Radio11 = GUICtrlCreateRadio("全选", $select_left+260, $user_top+85, $select_width, $select_height)
	   Global $Radio12 = GUICtrlCreateRadio("全不选", $select_left+320, $user_top+85, $select_width+10, $select_height)
	
	   ;;创建一个按钮，语法：GUICtrlCreateButton ( "文本", 左侧, 上方 [, 宽度 [, 高度 [, 风格 [, 扩展风格]]]] )
	   Global $Button1 = GUICtrlCreateButton("运行 (&A)", $select_left, $user_top+80, $select_width+20, $select_height)
	   Global $Button2 = GUICtrlCreateButton("退出 (&E)", $select_left+410, $user_top+80, $select_width+20, $select_height)
	
	   ;;窗口关闭事件
	   GUISetOnEvent($GUI_EVENT_CLOSE,"_exit")
	
	EndFunc
	
	;;闲置工作，不做任何事
	While 1
	   sleep(1000)
	WEnd
	
	;;关闭程序
	Func _exit()
	   Exit
	EndFun

双击运行脚本，即可见创建好的GUI

![](/images/20170502/170502_01.jpg)