---
title: 【JAVA】Linux命令行下载JDK
tags:
  - linux
  - java
  - jdk
categories: 点滴随笔
photos: /images/photos/java.jpg
date: 2018-10-06 16:55:51
description:
---

#### 上传JDK到Linux的三种常用方法

通常安装jdk时，都是将jdk安装包下载至本地后在上传至服务器上进行安装，上传方式一般三种：

* lrzsz的rz工具从本地上传
* sftp从本地长传
* scp从其他服务器上拷贝

#### 下载JDK到Linux时失败

之前在Windows上下载jdk安装包时除了网络波动下载速度极慢之外，没遇到过其他问题，今天尝试wget和curl直接将jdk安装包下载到CentOS时，却一直失败，401、403、404各种报错：

    # curl -O http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
      0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0

    # wget http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm?AuthParam=1538820273_a811f9ca13d83fc7e47adff51160ed0b
    --2018-10-06 15:25:41--  http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm?AuthParam=1538820273_a811f9ca13d83fc7e47adff51160ed0b
    Resolving download.oracle.com (download.oracle.com)... 23.45.156.151
    Connecting to download.oracle.com (download.oracle.com)|23.45.156.151|:80... connected.
    HTTP request sent, awaiting response... 403 Forbidden
    2018-10-06 18:11:37 ERROR 403: Forbidden.

<!--more-->

    # wget http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm
    --2018-10-06 15:25:06--  http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm
    Resolving download.oracle.com (download.oracle.com)... 23.11.244.254
    Connecting to download.oracle.com (download.oracle.com)|23.11.244.254|:80... connected.
    HTTP request sent, awaiting response... 302 Found
    Location: http://101.110.118.23/download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm [following]
    --2018-10-06 15:25:06--  http://101.110.118.23/download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm
    Connecting to 101.110.118.23:80... connected.
    HTTP request sent, awaiting response... 302 Moved Temporarily
    Location: https://edelivery.oracle.com/akam/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm [following]
    --2018-10-06 15:25:07--  https://edelivery.oracle.com/akam/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm
    Resolving edelivery.oracle.com (edelivery.oracle.com)... 184.26.250.202, 2600:140b:2:580::2d3e, 2600:140b:2:58e::2d3e
    Connecting to edelivery.oracle.com (edelivery.oracle.com)|184.26.250.202|:443... connected.
    HTTP request sent, awaiting response... 302 Moved Temporarily
    Location: https://login.oracle.com:443/oam/server/osso_login?Site2pstoreToken=v1.2~EC062A1473FF0629A4~8946A20C284B232081A053BFB257652B3909A003A5DE47665678E12480A4E2E1A28B9F6404053DE86A278AA240146793464CC89C0636EFFA145C71219B38F8D03D2C1EB9B4F0B6E29FBE831217365A7B185FEA9BE67EA126EA4C1333E619CD105E547B9F39693835EAF131AAF6E9FE8BD516396EC3BDB6AA629927F8591CF083D50589B5A82B3F1EF5EDEB5BCF922338CD3EE3F87606FE1E7A546C76E8AEE6CDBCEE103719E3B97DF791658DF0A59930DA922D5118809C37FB0470DB811A485EBF4739263CD9FCA3E8B29EB85E1744836962F446EFF6E14E99696166292C2871D0D353719303B1611640578F17E33B98 [following]
    --2018-10-06 15:25:08--  https://login.oracle.com/oam/server/osso_login?Site2pstoreToken=v1.2~EC062A1473FF0629A4~8946A20C284B232081A053BFB257652B3909A003A5DE47665678E12480A4E2E1A28B9F6404053DE86A278AA240146793464CC89C0636EFFA145C71219B38F8D03D2C1EB9B4F0B6E29FBE831217365A7B185FEA9BE67EA126EA4C1333E619CD105E547B9F39693835EAF131AAF6E9FE8BD516396EC3BDB6AA629927F8591CF083D50589B5A82B3F1EF5EDEB5BCF922338CD3EE3F87606FE1E7A546C76E8AEE6CDBCEE103719E3B97DF791658DF0A59930DA922D5118809C37FB0470DB811A485EBF4739263CD9FCA3E8B29EB85E1744836962F446EFF6E14E99696166292C2871D0D353719303B1611640578F17E33B98
    Resolving login.oracle.com (login.oracle.com)... 156.151.58.18
    Connecting to login.oracle.com (login.oracle.com)|156.151.58.18|:443... connected.
    HTTP request sent, awaiting response... 401 Authorization Required
    Authorization failed.

#### 下载JDK到Linux服务器的方法

经过一番Google和尝试，找到在Linux下下载JDK安装包的正确方法，这依然需要在浏览器中找到所需jdk版本的下载链接，然后将下载链接拷贝到xshell中使用，以jdk1.8.0_111为例

##### 注册Oracle账号(略)

##### 找到所需的JDK版本

访问[Oracle Java Archive](https://www.oracle.com/technetwork/java/javase/archive-139210.html)，选择"Java SE 8"

![](/images/20181006/20181006_01.png)

在"Java SE 8 Archive Downloads"页面搜索"111"，定位到jdk8u111下载链接

![](/images/20181006/20181006_02.png)

##### 接受许可协议

点击"Accept License Agreement"接受许可协议

![](/images/20181006/20181006_03.png)

![](/images/20181006/20181006_04.png)

##### 点击下载链接

此时已经找到rpm或tar.gz包下载链接，如果右键直接复制链接，使用wget下载是会失败的，报401错误，"Authorization failed"

这里应该点击所需的的下载链接

Linux x64	158.35 MB  	[jdk-8u111-linux-x64.rpm](http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm)
Linux x64	173.04 MB  	[jdk-8u111-linux-x64.tar.gz](http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz)

##### 登录Oracle账户

页面会自动跳转到Oracle登录界面，登录之后再会开始下载，如果之前已经登录过，则会直接开始下载

![](/images/20181006/20181006_05.png)

![](/images/20181006/20181006_06.png)

##### 获取有效的下载链接

在浏览器下载进度条上右键，点击"复制下载链接"

![](/images/20181006/20181006_07.png)

##### 在终端中使用有效链接下载

wget使用了`-O`参数重命名下载文件名为"jdk-8u111-linux-x64.rpm"，否则文件名将包含链接中的参数字符串：jdk-8u111-linux-x64.rpm?AuthParam=1538822525_74f1e5b197a543ce709b12f740de6bc4

    # wget -O jdk-8u111-linux-x64.rpm  http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm?AuthParam=1538821644_f36fcd3cc8d4c3cc45bf2392cfb46f03
    --2018-10-06 18:25:33--  http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm?AuthParam=1538821644_f36fcd3cc8d4c3cc45bf2392cfb46f03
    Resolving download.oracle.com (download.oracle.com)... 23.45.156.151
    Connecting to download.oracle.com (download.oracle.com)|23.45.156.151|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 166040563 (158M) [application/x-redhat-package-manager]
    Saving to: ‘jdk-8u111-linux-x64.rpm’

    100%[=============================================================>] 166,040,563 11.0MB/s   in 16s    

    2018-10-06 18:27:08 (10.2 MB/s) - ‘jdk-8u111-linux-x64.rpm’ saved [166040563/166040563]

curl使用了`-o`参数重命名下载文件名为"jdk-8u111-linux-x64.tar.gz"，否则文件名将包含链接中的参数字符串

    # curl -o jdk-8u111-linux-x64.tar.gz http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz?AuthParam=1538823707_795a66a88de4a78316aed1043efdce02
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
    100  173M  100  173M    0     0   9.9M      0  0:00:17  0:00:17 --:--:-- 10.7M

    # ls
    anaconda-ks.cfg  jdk-8u111-linux-x64.rpm  jdk-8u111-linux-x64.tar.gz

##### 该下载链接仅5分钟内有效

经多次尝试，确认下载链接只在生成后的5分钟内有效，期间可以多次使用来下载，超过5分钟则会报错"ERROR 403: Forbidden"

    # wget -O jdk-8u111-linux-x64.rpm http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm?AuthParam=1538821644_f36fcd3cc8d4c3cc45bf2392cfb46f03
    --2018-10-06 18:30:29--  http://download.oracle.com/otn/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm?AuthParam=1538821644_f36fcd3cc8d4c3cc45bf2392cfb46f03
    Resolving download.oracle.com (download.oracle.com)... 23.45.156.151
    Connecting to download.oracle.com (download.oracle.com)|23.45.156.151|:80... connected.
    HTTP request sent, awaiting response... 403 Forbidden
    2018-10-06 18:30:29 ERROR 403: Forbidden.

---

#### 参考文档

* Oracle Java Archive ：[https://www.oracle.com/technetwork/java/javase/archive-139210.html](https://www.oracle.com/technetwork/java/javase/archive-139210.html)

* How To Install Java on CentOS and Fedora ：[https://www.digitalocean.com/community/tutorials/how-to-install-java-on-centos-and-fedora#install-oracle-java-8](https://www.digitalocean.com/community/tutorials/how-to-install-java-on-centos-and-fedora)

