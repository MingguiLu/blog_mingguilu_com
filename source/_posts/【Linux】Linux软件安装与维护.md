---

date: 2016-12-30T09:09:19+08:00
description: ""
tags:
- Linux
- apt
- apt-get
- rpm
- yum
title: 【Linux】Linux软件安装与维护
categories: 点滴随笔
photos: /images/photos/linux_software.jpg

---

### 使用软件源码包

#### 跨代码文件的依赖关系

手动编译难度高，需要使用项目管理器：

- c、c++: make (configure + Makefile.in ——> makefile)
	- 源代码 ——> 预处理 ——> 编译(gcc) ——> 汇编 ——> 链接 ——> 执行
- java: maven

#### 开发工具

- autoconf： 生成configure脚本
- automake： 生成Makefile.in

#### 编译安装三步骤

- ./configure: 
	- 通过选项传递参数，指定启用特性、安装路径等；执行时参数结合Makefile.in来生成makefile
	- 检查依赖的外部环境
- make
	- 根据makefile构建应用程序
- make install
	- 实际执行脚本，将构建好的二进制应用程序和库文件复制(install命令)到指定的目录中去

#### 编译c源代码

- 开发工具： make、gcc等
- 开发环境： 开发库、头文件
	- glibc: 标准库
- 通过“包组”提供开发组件
	- CentOS 5： Development Tools、Development Library 
	- CentOS 6： Development Tools、Server Platform Development
	- CentOS 7:  Development Tools
		- yum groupinstall 'Development Tools'

#### configure脚本

- ./configure --help 查看配置文件参数
	- Configuration 对./configure脚本本身运行的过程进行配置
	- Installation directories 程序安装目录
		- --prefix=DIR 指定默认安装路径，默认是/usr/local
		- --sysconfdir=DIR 配置文件安装路径,默认是 PREFIX/etc
	- Program names 改变安装后程序的名称
	- System types 形成各个平台的安装版本，即交叉编译
	- Optional Features 可选特性
		- --disable-FEATURE
		- --enable-FEATURE[=ARG]
	- Optional Packages 可选包
		- --with-PACKAGE[=ARG]
  		- --without-PACKAGE 
	- Some influential environment variables 影响安装的环境变量

#### make

#### make install

#### 安装后的配置

##### 导出二进制的程序目录至PATH环境变量中

vim /etc/profile/NAME.sh
export PATH=/PATH/TO/BIN:$PATH

##### 导出库文件路径

vim /etc/ld.so.conf.d/NAME.conf
添加新的库文件所在目录之词文件中

让系统重新生成缓存： ldconfig [-v]

##### 导出头文件

基于链接的方式实现
ln -sv 

##### 导出帮助手册

vim /etc/man.conf
MANPATH /PATH/TO/MAN


### Debian/Ubuntu
Debian系Linux发行版的软件包格式为deb

#### dpkg
dpkg的是基于Debian系统的一个低级包管理器。它可以安装，删除，提供有关资料，以及建立*.deb包，但它不能自动下载并安装它们相应的依赖包。

<!--more-->

查看帮助

	dpkg --help

查看指定软件包的信息

	dpkg -I 下载/Software/vivaldi-stable_1.6.689.46-1_amd64.deb

	#以Vivaldi浏览器deb安装包为例
	##必须使用软件包的完整名称

列出指定软件包里的文件目录

	dpkg -c 下载/Software/vivaldi-stable_1.6.689.46-1_amd64.deb

	#必须使用软件包的完整名称

安装deb软件包

	dpkg -i vivaldi-stable_1.6.689.46-1_amd64.deb

列出所有已安装的软件包

	dpkg -l

查询是否已安装指定软件包

	dpkg -l vivaldi-stable

	#即可看到软件名称、版本、架构、描述信息
	#注意软件名称必须正确，比如查询“vivaldi”就会提示“dpkg-query: 没有找到与 vivaldi 相匹配的软件包”
	#当不能确认完整的软件包名称，可以再输入“vivaldi”后，按“Tab”键，会列出所有相关包名称

查看已安装的软件包的详细信息

	dpkg -s vivaldi-stable

	#同上，必须指定软件名称“vivaldi-stable”，而不能用原始的软件包名称“vivaldi-stable_1.6.689.46-1_amd64.deb”

列出指定软件包安装后的所有文件

	dpkg -L vivaldi-stable

	#同上

卸载软件包（保留配置文件）

	dpkg -r vivaldi-stable

	#同上，必须指定软件名称“vivaldi-stable”，而不能用原始的软件包名称“vivaldi-stable_1.6.689.46-1_amd64.deb”

卸载软件包（删除配置文件）

	dpkg -p vivaldi-stable

	#有些软件包卸载后还会遗留一些配置文件，使用`-p`就像在windows上卸载软件时勾选上“同时清除用户配置文件”

安装指定目录及其子目录下所有的“.deb”包

	dpkg -R --install debpackages/

	# -R 参数表示递归，指定目录和子目录下所有的.deb包都将被安装

释放软件包，但不配置

	dpkg --unpack vivaldi-stable_1.6.689.46-1_amd64.deb

重新配置软件包

	dpkg --configure

替换软件包信息

	dpkg --update-avail

清除包的当前可用信息

	dpkg --clear-avail

丢弃所有不能安装和不可用软件包的信息

	dpkg --forget-old-unavail

显示版本

	dpkg --version


#### apt-cache、apt-get
##### apt-cache
apt-cache是Linux下的apt软件包管理工具，使用它能查询到apt的二进制软件包缓存文件,结合一些参数使用能查寻到软件包信息和软件包依赖关系等

列出所有软件包的名称

	apt-cache pkgnames

列出所有以指定字符开头的软件包

	apt-cache pkgnames apache2

	#以“apache2”为例

列出包含指定关键字的软件包和简介

	apt-cache search apache2

以便于阅读的格式介绍该软件包

	apt-cache show apache2

查询软件包的依赖关系

	apt-cache showpkg apache2

统计全部软件包的信息

	apt-cache stats

##### apt-get
apt-get是Debian及其衍生版的高级包管理器，并提供命令行方式来从多个来源检索和安装软件包，其中包括解决依赖性。和dpkg不同的是，apt-get不是直接基于.deb文件工作，而是基于软件包的正确名称。

更新软件源（/etc/apt/source.list）

	sudo apt-get update

升级已安装的所有软件包（不管依赖性，不添加包，也不删除包）

	sudo apt-get upgrade

升级已安装的所有软件包（根据依赖关系的变化，添加包或删除包）

	sudo apt-get dist-upgrade

安装指定的软件包

	sudo apt-get install apache2

	#以“apache2”为例

安装多个软件包

	sudo apt-get install apache2 mysql-server php5

	#同时安装“apache2”、"mysql-server"、"php5"，并解决依赖性

安装包含指定字符串的软件包

	sudo apt-get install '*name*'

安装软件包但不升级

	sudo apt-get install packageName --no-upgrade

安装指定名称和版本的软件包

	sudo apt-get install apache2=2.4.18-2ubuntu3.1

卸载软件包但不清除配置文件

	sudo apt-get remove apache2

卸载软件包同时清除配置文件

	sudo apt-get purge apaceh2

或者

	sudo apt-get remove --purge apache2

清理本地软件仓库

	sudo apt-get clean

仅下载指定软件源码包

	sudo apt-get --download-only source apache2

下载指定软件包并解包

	sudo apt-get source apache2

同时下载、解包并编译软件包

	sudo apt-get --compile source apache2

仅下载不安装软件包

	sudo apt-get download apache2

查看软件包版本变更日志

	sudo apt-get changelog apache2

检查是否有损坏的依赖关系

	sudo apt-get check

建立指定软件包的编译环境

	sudo apt-get build-dep apache2

	#为手工编译软件apache2,提前把编译过程中需要用的软件包先安装配置好

将已经删除了的软件包的.deb安装文件从硬盘中删除掉

	sudo apt-get autoclean

将已经安装了的所有软件包的.deb包从硬盘中删除掉

	sudo apt-get clean

删除为了满足其他软件包的依赖而安装的，但现在不再需要的软件包

	sudo apt-get autoremove apache2
