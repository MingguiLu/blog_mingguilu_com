---
title: CentOS7安装Docker-EE和Docker-compose
description: ''
photos: /images/photos/docker.jpg
date: 2018-03-06 13:01:17
tags:
- Linux
- CentOS
- Docker
categories: 运维
---

#### 获取Docker EE repository URL

##### 注册Docker帐户

在[Docker官网](https://www.docker.com/)右上角点击`Create Docker ID`，输入`Docker ID`、邮箱、密码进行注册，随后在邮件点击链接激活帐户

![](/images/20180306/20180306_01_01.png)

<!--more-->

##### 在[Docker Store](https://store.docker.com/)获取`Docker EE for CentOS`

在`GET DOCKER`下面点击`GET DOCKER EE`，筛选出`Docker Enterprise Edition for CentOS`

![](/images/20180306/20180306_01_02.png)

Docker是付费版本，但可以免费体验1个月，点击`Start 1 Month Trial`

![](/images/20180306/20180306_01_03.png)

如果是第一次获取Docker repository URL，会要求完善个人账户信息，填写提交即可，接下来即可看到自己的URL

![](/images/20180306/20180306_01_04.png)

#### 卸载旧版本的`Docker`或`Docker-engine`

    $ sudo yum remove docker \
                    docker-client \
                    docker-client-latest \
                    docker-common \
                    docker-latest \
                    docker-latest-logrotate \
                    docker-logrotate \
                    docker-selinux \
                    docker-engine-selinux \
                    docker-engine \
                    docker-ce

#### 在线安装`Docker EE`

##### 配置安装源

###### 移除现有的Docker yum源文件

    [mingguilu@localhost yum.repos.d]$ sudo mv docker-ee.repo docker-ee.repo.bak
    [mingguilu@localhost yum.repos.d]$ ls
    CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo    CentOS-Vault.repo
    CentOS-CR.repo    CentOS-fasttrack.repo  CentOS-Sources.repo  docker-ee.repo.bak

###### 将Docker EE repository UR添加到临时环境变量中

    $ export DOCKERURL='<DOCKER-EE-URL>'

    [mingguilu@localhost yum.repos.d]$ export DOCKERURL='<DOCKER-EE-URL>'
    [mingguilu@localhost yum.repos.d]$ export | head -1
    declare -x DOCKERURL="https://storebits.docker.com/ee/centos/sub-43072c8f-268b-4608-8065-9996bb1c7129"

###### 

    $ sudo -E sh -c 'echo "$DOCKERURL/centos" > /etc/yum/vars/dockerurl'

###### 

    $ sudo yum install -y yum-utils \
    device-mapper-persistent-data \
    lvm2

######

    $ sudo -E yum-config-manager \
        --add-repo \
        "$DOCKERURL/centos/docker-ee.repo"

##### 正式安装

    $ sudo yum -y install docker-ee

    [mingguilu@localhost yum.repos.d]$ sudo yum install -y docker-ee
    已加载插件：fastestmirror
    docker-ee-stable-17.06                                       | 2.9 kB  00:00:00     
    docker-ee-stable-18.01                                       | 2.9 kB  00:00:00     
    (1/2): docker-ee-stable-18.01/x86_64/primary_db              | 1.1 kB  00:00:02     
    (2/2): docker-ee-stable-17.06/x86_64/primary_db              | 7.6 kB  00:00:02     
    Loading mirror speeds from cached hostfile
    * base: centos.ustc.edu.cn
    * extras: mirrors.sohu.com
    * updates: mirrors.sohu.com
    正在解决依赖关系
    --> 正在检查事务
    ---> 软件包 docker-ee.x86_64.0.17.06.2.ee.6-3.el7.centos 将被 安装
    --> 正在处理依赖关系 container-selinux >= 2.9，它被软件包 docker-ee-17.06.2.ee.6-3.el7.centos.x86_64 需要
    --> 正在检查事务
    ---> 软件包 container-selinux.noarch.2.2.36-1.gitff95335.el7 将被 安装
    --> 解决依赖关系完成

    依赖关系解决

    ====================================================================================
    Package           架构   版本                         源                      大小
    ====================================================================================
    正在安装:
    docker-ee         x86_64 17.06.2.ee.6-3.el7.centos    docker-ee-stable-17.06  25 M
    为依赖而安装:
    container-selinux noarch 2:2.36-1.gitff95335.el7      extras                  31 k

    事务概要
    ====================================================================================
    安装  1 软件包 (+1 依赖软件包)

    总下载量：25 M
    安装大小：79 M
    Downloading packages:
    (1/2): container-selinux-2.36-1.gitff95335.el7.noarch.rpm    |  31 kB  00:00:01     
    warning: /var/cache/yum/x86_64/7/docker-ee-stable-17.06/packages/docker-ee-17.06.2.ee.6-3.el7.centos.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID 76682bc9: NOKEY
    docker-ee-17.06.2.ee.6-3.el7.centos.x86_64.rpm 的公钥尚未安装
    (2/2): docker-ee-17.06.2.ee.6-3.el7.centos.x86_64.rpm        |  25 MB  00:00:48     
    ------------------------------------------------------------------------------------
    总计                                                   534 kB/s |  25 MB  00:48     
    从 https://storebits.docker.com/ee/centos/sub-43072c8f-268b-4608-8065-9996bb1c7129/centos/gpg 检索密钥
    导入 GPG key 0x76682BC9:
    用户ID     : "Docker Release (EE rpm) <docker@docker.com>"
    指纹       : 77fe da13 1a83 1d29 a418 d3e8 99e5 ff2e 7668 2bc9
    来自       : https://storebits.docker.com/ee/centos/sub-43072c8f-268b-4608-8065-9996bb1c7129/centos/gpg
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    正在安装    : 2:container-selinux-2.36-1.gitff95335.el7.noarch                1/2 
    正在安装    : docker-ee-17.06.2.ee.6-3.el7.centos.x86_64                      2/2 
    验证中      : docker-ee-17.06.2.ee.6-3.el7.centos.x86_64                      1/2 
    验证中      : 2:container-selinux-2.36-1.gitff95335.el7.noarch                2/2 

    已安装:
    docker-ee.x86_64 0:17.06.2.ee.6-3.el7.centos                                      

    作为依赖被安装:
    container-selinux.noarch 2:2.36-1.gitff95335.el7                                  
    完毕！

##### 启动Docker

    $ sudo systemctl start docker

#### 测试Docker

    [mingguilu@localhost ~]$ sudo docker run hello-world

    Hello from Docker!
    This message shows that your installation appears to be working correctly.

    To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        (amd64)
    3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

    To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash

    Share images, automate workflows, and more with a free Docker ID:
    https://cloud.docker.com/

    For more examples and ideas, visit:
    https://docs.docker.com/engine/userguide/

#### 安装Docker Compose

##### 安装最新版的Docker Compose

    $ sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

    [mingguilu@localhost ~]$ sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    [sudo] mingguilu 的密码：
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
    100   617    0   617    0     0     68      0 --:--:--  0:00:08 --:--:--   138
    100 8288k  100 8288k    0     0   106k      0  0:01:17  0:01:17 --:--:--  319k

##### 添加可执行权限

[mingguilu@localhost ~]$ sudo chmod +x /usr/local/bin/docker-compose 
[sudo] mingguilu 的密码：
[mingguilu@localhost ~]$ 
[mingguilu@localhost ~]$ ls -lh /usr/local/bin/docker-compose 
-rwxr-xr-x. 1 root root 8.1M 3月   6 12:58 /usr/local/bin/docker-compose

##### 查看docker-compose版本

    [mingguilu@localhost ~]$ docker-compose --version
    docker-compose version 1.19.0, build 9e633ef

---

#### 参考文档：

##### 《Install Docker》:  [https://docs.docker.com/install/](https://docs.docker.com/install/)

##### 《Get Docker EE for CentOS》:  [https://docs.docker.com/install/linux/docker-ee/centos/](https://docs.docker.com/install/linux/docker-ee/centos/)

##### 《Install Docker Compose》:  [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)

