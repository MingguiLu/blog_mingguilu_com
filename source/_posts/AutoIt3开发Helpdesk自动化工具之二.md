---
date: 2017-04-21T09:11:59+08:00
description: ""
tags: 
- AutoIt
- AutoIt3
- Helpdesk
- 自动化
title: AutoIt3开发Helpdesk自动化工具之二:常用语法
categories: 运维之道
photos: /images/photos/autoit3.png
---

在正式开始脚本工具编写之前，先熟悉一下AutoIt3常用的语法

#### 参考文档：
 
* AutoIt3安装目录中的官方英文手册 v3.3.14.2 ： AutoIt.chm  
* AutoIt 在线手册中文版_脚本之家 v3.1.1  :  http://www.jb51.net/shouce/autoit/   

<!--more-->

#### 编辑器

自带的编辑器是SciTe，带有所有的语法高亮功能，同时还整合了很多AutoIt的第三方工具（比如语法错误检查和脚本整理等）

AutoIt3脚本文件的后缀名是`.au3`，在安装AutoIt软件的系统上可直接运行，在未安装的系统上需编译为`.exe`程序。创建`.au3`格式的脚本文件方法：

* 右键 ——> 新建 ——> AutoIt v3 Script 

建议使用这种方法，优势在于脚本会自动添加一段头注释模板：

	#cs ----------------------------------------------------------------------------

	 AutoIt Version: 3.3.14.2
	 Author:         myName

	 Script Function:
		Template AutoIt script.

	#ce ----------------------------------------------------------------------------

	; Script Start - Add your code below here

* 在编辑器上 File ——> New、直接点击New图标、或者使用快捷键 `Ctrl + N`，这些不会自动添加头注释

#### 注释

* 单行注释	分号“;”之后的内容作为注释内容
* 多行注释	

	#comments-start	注释开始，可简写为：#cs

	;code	脚本代码

	#comments-end	注释结束，可简写为：#ce
	
#### 暂停执行脚本

	Sleep ( 暂停时间 )	;暂停执行的时间以毫秒为单位，1000毫秒 = 1秒
	
	Sleep(500)
	;推荐在每次等待窗口出现之后和模拟键鼠操作控件时使用Sleep暂停执行脚本半秒钟，以优化自动任务的可视效果
	
#### 运行外部程序

 	Run ( "文件名" [, "工作目录" [, 标志]] )
 	
 *  .exe 可执行程序
 	
 	Run("notepad.exe")
 	
 	Run("C:\Program Files\Microsoft Office\Office16\OUTLOOK.EXE")	;绝对路径
 	
 	Run("\\\\wh-filesrv-01\Installers\LinPhone\Linphone-3.10.2-win32.exe")	;网络共享路径
 	
 * .msi	微软格式的安装包
 
 	Run("msiexec /i \\\\wh-filesrv-01\Installers\Synjones\二代证读验机具USB驱动\USBDrv3.0-x64.msi")	
 	;调用系统程序`msiexec.exe`操作.msi程序， `/i`参数表示安装
 	 
 * .cpl 	控制面板项
 
	Run("control sysdm.cpl")	;打开系统属性窗口
	
* .cer	证书文件

	Run("explorer  \\\\wh-filesrv-01\Installers\AD\CA.cer")	;打开安装证书窗口
	
* DOS命令 

	 Run("net user admin abc@123 /add")	;创建本地用户admin，密码为abc@123
	 
	 Run("net localgroup users admin /add")	;将本地用户admin加入users组

*  系统目录

	Run("explorer " & @ProgramFilesDir)	;打开C:\Program Files (x86)

#### 以其它用户运行外部程序
	
	RunAs ( "用户名", "域", "密码", 注册标志, "程序" [, "工作目录" [, 显示标志 [, 选项标志 ]]] )
	;注册标志：0 - 不加载配置文件的交互式登录. 1 - 加载配置文件的交互式登录. 2 - 使用网络证书. 4 - 继承调用进程的环境, 而不是用户环境
	
* 以本地管理员Administrator运行

	Global $rootUserName = "administrator"  
	Global $rootPassword = "password@123456"  
	Local $tempUserName = @UserName  
   	
   	RunAs($rootUserName,@ComputerName,$rootPassWord,0,"\\\\wh-filesrv-01\Installers\LinPhone\Linphone-3.10.2-win32.exe","")
	
	RunAs($rootUserName,@ComputerName,$rootPassword,0,"net localgroup /delete  administrators " & $tempUserName,"")
	
	RunAs($rootUserName,@ComputerName,$rootPassWord,0,"certutil -addstore -f Root \\\\wh-filesrv-01\Installers\AD\CA.cer","")

#### 使用ShellExecute运行外部程序

	ShellExecute ( "文件名" [, "参数" [, "工作目录" [, "动作" [, 显示标志]]]] )
	;理论上windows能识别的文件，即双击能打开的文件，使用ShellExecute也能打开
	
* .msi 微软格式的安装包

	ShellExecute("\\\\wh-filesrv-01\Installers\Synjones\二代证读验机具USB驱动\USBDrv3.0-x64.msi")	;安装共享目录的.msi程序

* .msc 微软管理控制台窗口

	ShellExecute(@SystemDir & "\compmgmt.msc")	;打开计算机管理

* .docx、.xlsx、.pptx  Office文档

	ShellExecute("test.docx")	;打开当前目录中的Word文件
	ShellExecute("test.xlsx")	;打开当前目录中的Excel文件
	ShellExecute("test.pptx")	;打开当前目录中的PPT文件

* .lnk	快捷方式

	ShellExecute(@UserProfileDir & "\desktop\微信.lnk")	;通过快捷方式运行微信
	
#### 窗口操作

* 最小化所有窗口

	WinMinimizeAll()	
	;我习惯在每个任务开头加上这个函数，以便仅显示任务的活动窗口

* 等待指定窗口出现
	
	WinWait ( "窗口标题" [, "窗口文本" [, 超时时间]] )
	
* 激活指定的窗口

	WinActivate ( "窗口标题" [, "窗口文本"] )
	
* 等待指定的窗口被激活
	
	WinWaitActive ( "窗口标题", ["窗口文本"], [超时时间] )
	
* 关闭指定的窗口

	WinClose ( "窗口标题" [, "窗口文本"] )

* 等待指定的窗口被关闭

	WinWaitClose ( "窗口标题" [, "窗口文本" [, 超时时间]] )
	
* 检查指定的窗口是否存在

	WinExists ( "窗口标题" [, "窗口文本"] ) 	
	;返回值：若目标窗口确实存在则返回值为1，否则返回值为0

* 检查指定的窗口是否存在且当前被激活

	WinActive ( "窗口标题" [, "窗口文本"] ) 	
	;返回值：若目标窗口确实存在并被激活了则返回1，否则返回值为0

* 获取指定窗口的完整标题名

	WinGetTitle ( "窗口标题" [, "窗口文本"] ) 	
	;返回值：返回一个含有目标窗口的完整标题的字符串。若无匹配窗口则返回值为1

* 获取指定窗口中的文本

	WinGetText ( "窗口标题" [, "窗口文本"] ) 	
	;返回值：返回一个含有获得的窗口文本的字符串。若无匹配窗口则返回值为1

#### 控件操作

* 向指定控件发送鼠标点击命令

	ControlClick ( "窗口标题", "窗口文本", "控件ID" [, 按钮] [, 点击次数]] )  
	;按钮：默认“left”，还可以是“right”、“middle”；点击次数：默认“1”

* 选中或撤销选中指定的复选/单选框

	ControlCommand ( "窗口标题", "窗口文本", 控件ID, "命令", "选项" )  
	;命令：“Check”使目标按钮（复选框/单选框）变为选中状态 ;“UnCheck”撤销目标按钮（复选框/单选框）的选中状态
	
* 向指定的控件发送字符串

	ControlSend ( "窗口标题", "窗口文本", 控件ID, "字符串" [, 标志] )  
	;向控件发送指定的字符串，并追加到原有字符串后面

* 修改指定控件的文本

	ControlSetText( "窗口标题", "窗口文本", 控件ID, "新文本" )  
	;向控件发送指定的字符串，并替换原有的字符串
	
#### 模拟键击操作

参考文档：[Send - AutoIt 在线手册中文版_脚本之家](http://www.jb51.net/shouce/autoit/AutoIt_CN/html/functions/Send.htm)

	Send ( "按键" [, 标志] )  
	;向激活窗口发送模拟键击操作
	
	!  表示 Alt  
	+ 表示 Shift  
	^ 表示 Ctrl  
	# 表示 Windows  
	
	Send("!y")  ;按下Alt+Y键
	Send("^s")  ;按下Ctrl+S键
	Send("#r")  ;按下Win+R键，打开运行
	
	Send("{TAB}")  ;按下Tab键  
	Send("+{TAB 4}")  ;连续按下4次Shift+Tab键  
	Send("{ENTER}")  ;按下回车键  
	Send("{SPACE}")  ;按下空格键  
	  
	Send("{UP}")  ;方向键上  
	Send("{DOWN 3}")  ;连续按3次方向键下  
	Send("{LEFT}")  ;方向键左  
	Send("{RIGHT 5}")  ;连续按5次方向键右  

#### 消息框

	MsgBox ( 标志, "标题", "文本" [, 超时时间] )  
	;显示一个简单的对话框（可设置超时属性）
	
	$i = 5
	While $i > 0
	   MsgBox(64, "测试", "此对话框将会在" & $i & "秒后自动消失",1)
	   Sleep(1000)
	   $i = $i - 1
	WEnd
	
#### 输入框

	InputBox ( "标题", "提示信息" [, "默认数据" [, "密码字符" [, 宽度, 高度 [, 左边, 上边 [, 超时时间]]]]] )  
	;显示一个输入框以供用户输入数据
	
	$yourName = InputBox("提问","请输入你的姓名：","Please enter your name")
	MsgBox(64,"欢迎","Wellcome, " & $yourName & " !")

#### 检查当前用户是否拥有管理员权限

	IsAdmin()
	; 返回值为 1，说明当前用户拥有管理员权限  ;返回值为0，说明用户不是管理员
	
	If IsAdmin() Then
	   MsgBox(64,"提示","当前用户为系统管理员！请继续操作！")
	Else
	   MsgBox(64,"提示","当前用户非系统管理员！请切换管理员操作！")
	EndIf
	
#### 检查某个字符串是否含有给定的子串

	StringInStr ( "字符串", "子串" [, 区分大小写 [, 出现次序]] )
	;成功：返回子串的位置  ;失败：返回值为0，说明未发现匹配子串
	
尝试运行2次下面的脚本示例：

	WinMinimizeAll()
	Run("notepad.exe")
	WinWaitActive("无标题 - 记事本")
	Sleep(500)
	ControlSend("无标题 - 记事本","","Edit1","写入一段演示的文本内容！")
	Sleep(500)
	$tempText = WinGetText("")
	If StringInStr($tempText,"一段演示") Then
	   MsgBox(64,"提示","文本写入成功",1)
	EndIf
	Sleep(500)
	WinClose("无标题 - 记事本")
	WinWaitActive("记事本","保存(&S)")
	Sleep(500)
	Send("!s")
	WinWaitActive("另存为","命名空间树控件")
	Sleep(500)
	ControlSetText("另存为","命名空间树控件","Edit1","test.txt")
	Sleep(500)
	Send("!s")
	Sleep(500)
	If WinExists("确认另存为") Then
	   ;WinActivate("确认另存为")
	   ;WinWaitActive("确认另存为")
	   Send("!y")
	EndIf	
	
#### 操作注册表

* 读取注册表指定的值

	RegRead ( "键名", "值项" )
	
* 创建一个主键、子键或值项

	RegWrite ( "键名" [,"值项", "类型", 数据] )
	
* 从注册表中删除指定键值

	RegDelete ( "键名" [, "值项"] )
	
	;修改注册表实现开机自动登录：

	RegWrite("HKEY_LOCAL_MACHINE64\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon", "AutoAdminLogon", "REG_SZ", "1")  
	RegWrite("HKEY_LOCAL_MACHINE64\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon", "DefaultDomainName", "REG_SZ", $domainName)  
	RegWrite("HKEY_LOCAL_MACHINE64\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon", "DefaultUserName", "REG_SZ", $userName)  
	RegWrite("HKEY_LOCAL_MACHINE64\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon", "DefaultPassword", "REG_SZ", $userPassword)  

	;修改注册表取消开机自动登录：
	
	RegWrite("HKEY_LOCAL_MACHINE64\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon", "AutoAdminLogon", "REG_SZ", "0")  
	RegWrite("HKEY_LOCAL_MACHINE64\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon", "DefaultDomainName", "REG_SZ", ".")  
	RegWrite("HKEY_LOCAL_MACHINE64\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon", "DefaultUserName", "REG_SZ","administrator")  
	RegDelete("HKEY_LOCAL_MACHINE64\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon", "DefaultPassword")  

注意！操作注册表有一个技巧十分重要，如果是Windows x64系统，需在“HKEY_LOCAL_MACHINE”后加上“64”，否则操作会被重定向至 “HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node”

#### 关机

	Shutdown ( 代码 )
	
	0 = Logoff（注销）
	1 = Shutdown（关机）
	2 = Reboot（重启）
	4 = Force（强制执行）
	8 = Power down（断电）
	32= Suspend（待机）
	64= Hibernate（休眠）
	可按需把相应数值相加。比如，要关机并断电，则应指定数值 9 （shutdown + power down = 1 + 8 = 9）

