---
date: 2017-04-18T16:30:57+08:00
description: ""
tags: 
- AutoIt
- AutoIt3
- Helpdesk
- 自动化
title: AutoIt3开发Helpdesk自动化工具之一:简介
categories: 运维之道
photos: /images/photos/autoit3.png
---

在员工数量较多的大中型公司，Hekpdesk工程师每天重复处理大量简单而繁琐的桌面软硬件请求和报障，这些工作大多在初期进行短暂的技术学习后，很快演变为经验积累，但再丰富的经验如果无法解放时间和双手，枯燥和疲惫一直如影随形。细想一下，有些工作场景中Helpdesk工程师像机器一样在每台PC上按照相同的流程，打开相同的窗口，安装相同的软件，设置相同的选项.......听起来让人很不安？但这样的情景时常上演，比如需要给30名新员工配置办公电脑、部门需要统一安装某几款软件并进行设置、或者反过来要卸载某款软件......那么何必把自己当做机器呢，电脑本身不就是一台聪明的机器吗！

 接下来，我们将使用AutoIt3来开发一个让电脑自己执行重复任务的自动化脚本工具，并打造一个简洁实用的图形界面。

 <!--more-->

###  一、[AutoIt v3 简介](https://www.autoitscript.com/site/autoit/)

AutoIt v3是一种免费的基于类BASIC的脚本语言，用于自动化Windows GUI和通用脚本。 它使用模拟键盘，鼠标移动和窗口/控制操作的组合，以便以其他语言（例如VBScript和SendKeys）不可能或不可靠的方式自动执行任务。 AutoIt也非常小，独立，将运行在所有版本的Windows开箱即用，不需要烦人的“runtime”要求！

AutoIt最初设计用于PC“大批量交付”情况，可靠地自动化并配置成千上万的个人电脑。 随着时间的推移，它已经成为一种强大的语言，它支持复杂的表达式，用户自定义函数，循环以及经验丰富的脚本编写期望。

####  AutoIt v3 的优秀特性:    
 <p>
 
* 简单易学的类BASIC语法
* 模拟按键和鼠标移动
* 操作窗口和进程
* 与所有标准的Windows控件进行交互
* 脚本可以编译成独立的可执行文件
* 创建图形用户界面（GUI）
* 支持COM
* 正则表达式
* 直接调用外部DLL和Windows API函数
* Scriptable RunAs函数
* 详细的帮助文件和大型社区支持论坛
* 兼容Windows XP / Server 2003 / Vista / Windows 7 / Server 2008 / Server  2008 R2 / Windows 8 / Server 2012 R2
* 支持Unicode和x64
* 支持数字签名
* 适用于Windows Vista的用户帐户控制（UAC）

最重要的是，AutoIt将持续免费 - 但是如果你想支持在项目和网络托管上花费的时间，金钱和精力，那么你可以[捐赠](https://www.autoitscript.com/site/donate/)。

####  搭建AutoIt v3 脚本开发环境
<p>
[下载AutoIt v3 开发工具](https://www.autoitscript.com/site/autoit/downloads/)

下载时建议选择AutoIt3完全安装程序，它包含了：

* AutoIt 脚本运行软件、文档和实例
* Aut2Exe - 将可执行脚本转换为独立的.exe文件
* AutoitX - DLL/COM control。 具体作用我也不明白，安装后该目录中存放着写.dll文件
* Editor - 一个删减版本的SciTE脚本编辑器
* Au3Info - 用于探测指定窗口的详细信息，包括窗口大小和坐标、标题和文本、控件类型和ID等

安装时以缺省配置安装即可，安装完成即可获得我们所需的全部环境

###  二、项目简介
接下来将使用AutoIt3开发一个“Helpdesk自动化工具”，并打造一个简洁实用的用户图形界面以便交互。

![](/images/20170418/170418_01_02_01.png)

#### 主要用途

 用于新员工入职后电脑的自动化配置

#### 功能实现

* 随时随地开箱即用：通过文件共享在内网每一台PC上运行本工具和软件，并兼容各种意外的情况

我们更希望完全实现无人值守，但这并不容易。由于AutoIt3通常使用标题和文本来等待、激活、操作窗口，所以意外窗口使得自动化过程很脆弱。管理员用户和普通用户下、软件重复安装时、电脑运行速度、甚至工具本身都可能造成意外窗口，这需要反复调试来使脚本兼容各种场景。

* 自动进行多任务的组合：在图形界面上通过点击预设的操作选项和用户选项，自动勾选所需的功能模块

如上图，点击“驻地(含VPN)”，会自动勾选上所需的系统配置、软件安装项和自动重启系统+登录域账户选项，在点击“运行”后自动配置安装，并自动重启，自动登录用户账户；当用户帐号登陆后，再次运行工具勾选对应的“用户选项”进行outlook、skype等配置

* 自动进行系统配置： 为了实现桌面统一规范化管理或应对之后可能面临的需求

例如：创建一个本地用户Admin、添加IT组至本地管理员组	

公司在各地开设分职场，但由于员工数量较少未安排驻留的Helpdesk工程师，所有的需求和故障通过电话或远程桌面进行支持，为了应对电脑出现脱域无法登录，或本地管理员密码被爆破的情况，可以在初始配置时创建一个本地admin用户，加入users组，以便用户能顺利登录桌面提供TeamViewer远程授权；而事先将Helpdesk组加入电脑本地管理员组，可在远程到用户桌面后通过切换用户，夺回管理员权限并重置密码

* 自动进行软件安装：为了安装多个软件组合时，自动启动安装程序，自动配置选项直至安装完成

一般我们会将操纵系统和常用的办公软件通过MDT做统一部署，比如Office、输入法、PDF阅读器、桌面词典等，而各部门专用的业务软件则会在配置电脑时进行安装