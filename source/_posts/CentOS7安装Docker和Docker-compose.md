---
title: CentOS7安装Docker和Docker-compose
description: ''
photos: /images/photos/docker.jpg
date: 2018-03-06 13:01:17
tags:
- Linux
- CentOS
- Docker
categories: 运维之道
---

Docker分为Docker-EE(enterprise edition)和Docker-CE(community edition)两个版本；EE是收费版本，由官方技术团队维护和提供技术支持；CE是免费版本，由社区维护和提供技术支持

<!--more-->

#### 安装Docker-EE

##### 获取Docker EE repository URL

###### 注册Docker帐户

在[Docker官网](https://www.docker.com/)右上角点击`Create Docker ID`，输入`Docker ID`、邮箱、密码进行注册，随后在邮件点击链接激活帐户

![](/images/20180306/20180306_01_01.png)

<!--more-->

###### 在[Docker Store](https://store.docker.com/)获取`Docker EE for CentOS`

在`GET DOCKER`下面点击`GET DOCKER EE`，筛选出`Docker Enterprise Edition for CentOS`

![](/images/20180306/20180306_01_02.png)

Docker-EE是付费版本，但可以免费体验1个月，点击`Start 1 Month Trial`

![](/images/20180306/20180306_01_03.png)

如果是第一次获取Docker repository URL，会要求完善个人账户信息，填写提交即可，接下来即可看到自己的URL

![](/images/20180306/20180306_01_04.png)

##### 卸载旧版本的`Docker`或`Docker-engine`

如果系统上不存在旧版的Docker可以跳过这步

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

##### 配置Yum源

###### 移除现有的Docker yum源

    [mingguilu@localhost yum.repos.d]$ sudo mv docker-ee.repo docker-ee.repo.bak
    [mingguilu@localhost yum.repos.d]$ ls
    CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo    CentOS-Vault.repo
    CentOS-CR.repo    CentOS-fasttrack.repo  CentOS-Sources.repo  docker-ee.repo.bak

###### 将Docker EE repository UR添加到临时环境变量中

    $ export DOCKERURL='<DOCKER-EE-URL>'

    [mingguilu@localhost yum.repos.d]$ export DOCKERURL='<DOCKER-EE-URL>'
    [mingguilu@localhost yum.repos.d]$ export | head -1
    declare -x DOCKERURL="https://storebits.docker.com/ee/centos/sub-43072c8f-268b-4608-8065-9996bb1c7129"

###### 把存储Docker EE repository UR的变量添加到/etc/yum/vars/dockerurl文件中

    $ sudo -E sh -c 'echo "$DOCKERURL/centos" > /etc/yum/vars/dockerurl'

###### 安装必需的软件yum-utils、device-mapper-persistent-data、lvm2

    $ sudo yum install -y yum-utils \
        device-mapper-persistent-data \
        lvm2

###### 添加到yum源

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

##### 开机自动启动Docker服务

    $ sudo systemctl enable docker

##### 查看Docker状态

    $ sudo systemctl status docker

##### 停止Docker

    $ sudo systemctl stop docker

##### 测试Docker

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

#### 安装Docker-CE

##### 在线安装Docker-CE

在线安装Docker-CE的步骤与Docker-EE差不多，参考官网文档：[Get Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)

##### 离线安装Docker-CE

###### 下载Docker-CE的rpm包

下载地址： [https://download.docker.com/linux/centos/7/x86_64/stable/Packages/](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)

    [mingguilu@localhost ~]$  wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-17.12.1.ce-1.el7.centos.x86_64.rpm


###### 安装Docker-CE

    [mingguilu@localhost ~]$ sudo yum install ./docker-ce-17.12.1.ce-1.el7.centos.x86_64.rpm

 
#### 安装Docker Compose

##### curl方式安装Docker Compose

###### curl命令安装 

    $ sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

    [mingguilu@localhost ~]$ sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    [sudo] mingguilu 的密码：
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
    100   617    0   617    0     0     68      0 --:--:--  0:00:08 --:--:--   138
    100 8288k  100 8288k    0     0   106k      0  0:01:17  0:01:17 --:--:--  319k

###### 添加可执行权限

    [mingguilu@localhost ~]$ sudo chmod +x /usr/local/bin/docker-compose 
    [sudo] mingguilu 的密码：
    [mingguilu@localhost ~]$ 
    [mingguilu@localhost ~]$ ls -lh /usr/local/bin/docker-compose 
    -rwxr-xr-x. 1 root root 8.1M 3月   6 12:58 /usr/local/bin/docker-compose

###### 查看docker-compose版本

    [mingguilu@localhost ~]$ docker-compose --version
    docker-compose version 1.19.0, build 9e633ef

##### pip方式安装

###### 安装python-pip

    [mingguilu@localhost ~]$ sudo yum install python-pip
    已加载插件：fastestmirror
    Loading mirror speeds from cached hostfile
    * base: mirrors.sohu.com
    * extras: mirrors.sohu.com
    * updates: mirrors.sohu.com
    没有可用软件包 python-pip。
    错误：无须任何处理

###### 找不到python-pip包，需先安装epel-release

    [mingguilu@localhost ~]$ sudo yum install -y epel-release 

    [mingguilu@localhost ~]$ sudo yum install -y python-pip

    [mingguilu@localhost ~]$ sudo pip install --upgrade pip

###### pip安装Docker-compose

    [mingguilu@localhost ~]$ sudo yum install -y docker-compose
    已加载插件：fastestmirror
    Loading mirror speeds from cached hostfile
    * base: mirrors.sohu.com
    * epel: mirror01.idc.hinet.net
    * extras: mirrors.sohu.com
    * updates: mirrors.sohu.com
    正在解决依赖关系
    --> 正在检查事务
    ---> 软件包 docker-compose.noarch.0.1.9.0-5.el7 将被 安装
    --> 正在处理依赖关系 python-docker-py >= 1.10.6-1，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-dockerpty >= 0.4.1-1，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 PyYAML，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-cached_property，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-docopt，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-enum34，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-jsonschema，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-requests，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-six，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-texttable，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python-websocket-client，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在处理依赖关系 python2-backports-functools_lru_cache，它被软件包 docker-compose-1.9.0-5.el7.noarch 需要
    --> 正在检查事务
    ---> 软件包 PyYAML.x86_64.0.3.10-11.el7 将被 安装
    --> 正在处理依赖关系 libyaml-0.so.2()(64bit)，它被软件包 PyYAML-3.10-11.el7.x86_64 需要
    ---> 软件包 python-docker-py.noarch.0.1.10.6-3.el7 将被 安装
    --> 正在处理依赖关系 python-ipaddress，它被软件包 python-docker-py-1.10.6-3.el7.noarch 需要
    --> 正在处理依赖关系 python-docker-pycreds，它被软件包 python-docker-py-1.10.6-3.el7.noarch 需要
    ---> 软件包 python-enum34.noarch.0.1.0.4-1.el7 将被 安装
    ---> 软件包 python-requests.noarch.0.2.6.0-1.el7_1 将被 安装
    --> 正在处理依赖关系 python-urllib3 >= 1.10.2-1，它被软件包 python-requests-2.6.0-1.el7_1.noarch 需要
    --> 正在处理依赖关系 python-chardet >= 2.2.1-1，它被软件包 python-requests-2.6.0-1.el7_1.noarch 需要
    ---> 软件包 python-six.noarch.0.1.9.0-2.el7 将被 安装
    ---> 软件包 python-websocket-client.noarch.0.0.32.0-116.el7 将被 安装
    ---> 软件包 python2-backports-functools_lru_cache.noarch.0.1.2.1-4.el7 将被 安装
    ---> 软件包 python2-cached_property.noarch.0.1.3.0-7.el7 将被 安装
    ---> 软件包 python2-dockerpty.noarch.0.0.4.1-9.el7 将被 安装
    ---> 软件包 python2-docopt.noarch.0.0.6.2-7.el7 将被 安装
    ---> 软件包 python2-jsonschema.noarch.0.2.5.1-3.el7 将被 安装
    --> 正在处理依赖关系 python-repoze-lru，它被软件包 python2-jsonschema-2.5.1-3.el7.noarch 需要
    ---> 软件包 python2-texttable.noarch.0.0.9.1-1.el7 将被 安装
    --> 正在检查事务
    ---> 软件包 libyaml.x86_64.0.0.1.4-11.el7_0 将被 安装
    ---> 软件包 python-chardet.noarch.0.2.2.1-1.el7_1 将被 安装
    ---> 软件包 python-docker-pycreds.noarch.0.1.10.6-3.el7 将被 安装
    ---> 软件包 python-ipaddress.noarch.0.1.0.16-2.el7 将被 安装
    ---> 软件包 python-repoze-lru.noarch.0.0.4-3.el7 将被 安装
    ---> 软件包 python-urllib3.noarch.0.1.10.2-3.el7 将被 安装
    --> 解决依赖关系完成

    依赖关系解决

    =========================================================================================================================================================================
    Package                                                      架构                          版本                                     源                             大小
    =========================================================================================================================================================================
    正在安装:
    docker-compose                                               noarch                        1.9.0-5.el7                              epel                          150 k
    为依赖而安装:
    PyYAML                                                       x86_64                        3.10-11.el7                              base                          153 k
    libyaml                                                      x86_64                        0.1.4-11.el7_0                           base                           55 k
    python-chardet                                               noarch                        2.2.1-1.el7_1                            base                          227 k
    python-docker-py                                             noarch                        1.10.6-3.el7                             extras                        101 k
    python-docker-pycreds                                        noarch                        1.10.6-3.el7                             extras                         18 k
    python-enum34                                                noarch                        1.0.4-1.el7                              base                           52 k
    python-ipaddress                                             noarch                        1.0.16-2.el7                             base                           34 k
    python-repoze-lru                                            noarch                        0.4-3.el7                                epel                           13 k
    python-requests                                              noarch                        2.6.0-1.el7_1                            base                           94 k
    python-six                                                   noarch                        1.9.0-2.el7                              base                           29 k
    python-urllib3                                               noarch                        1.10.2-3.el7                             base                          101 k
    python-websocket-client                                      noarch                        0.32.0-116.el7                           extras                         56 k
    python2-backports-functools_lru_cache                        noarch                        1.2.1-4.el7                              epel                           12 k
    python2-cached_property                                      noarch                        1.3.0-7.el7                              epel                           16 k
    python2-dockerpty                                            noarch                        0.4.1-9.el7                              epel                           28 k
    python2-docopt                                               noarch                        0.6.2-7.el7                              epel                           28 k
    python2-jsonschema                                           noarch                        2.5.1-3.el7                              epel                           75 k
    python2-texttable                                            noarch                        0.9.1-1.el7                              epel                           21 k

    事务概要
    =========================================================================================================================================================================
    安装  1 软件包 (+18 依赖软件包)

    总下载量：1.2 M
    安装大小：4.8 M
    Downloading packages:
    (1/19): libyaml-0.1.4-11.el7_0.x86_64.rpm                                                                                                         |  55 kB  00:00:00     
    (2/19): python-docker-pycreds-1.10.6-3.el7.noarch.rpm                                                                                             |  18 kB  00:00:00     
    (3/19): python-enum34-1.0.4-1.el7.noarch.rpm                                                                                                      |  52 kB  00:00:00     
    (4/19): PyYAML-3.10-11.el7.x86_64.rpm                                                                                                             | 153 kB  00:00:00     
    (5/19): python-docker-py-1.10.6-3.el7.noarch.rpm                                                                                                  | 101 kB  00:00:00     
    (6/19): python-ipaddress-1.0.16-2.el7.noarch.rpm                                                                                                  |  34 kB  00:00:00     
    (7/19): python-chardet-2.2.1-1.el7_1.noarch.rpm                                                                                                   | 227 kB  00:00:00     
    (8/19): docker-compose-1.9.0-5.el7.noarch.rpm                                                                                                     | 150 kB  00:00:00     
    (9/19): python-requests-2.6.0-1.el7_1.noarch.rpm                                                                                                  |  94 kB  00:00:00     
    (10/19): python-repoze-lru-0.4-3.el7.noarch.rpm                                                                                                   |  13 kB  00:00:00     
    (11/19): python2-backports-functools_lru_cache-1.2.1-4.el7.noarch.rpm                                                                             |  12 kB  00:00:00     
    (12/19): python-six-1.9.0-2.el7.noarch.rpm                                                                                                        |  29 kB  00:00:00     
    (13/19): python2-cached_property-1.3.0-7.el7.noarch.rpm                                                                                           |  16 kB  00:00:00     
    (14/19): python-urllib3-1.10.2-3.el7.noarch.rpm                                                                                                   | 101 kB  00:00:00     
    (15/19): python-websocket-client-0.32.0-116.el7.noarch.rpm                                                                                        |  56 kB  00:00:00     
    (16/19): python2-dockerpty-0.4.1-9.el7.noarch.rpm                                                                                                 |  28 kB  00:00:00     
    (17/19): python2-docopt-0.6.2-7.el7.noarch.rpm                                                                                                    |  28 kB  00:00:00     
    (18/19): python2-jsonschema-2.5.1-3.el7.noarch.rpm                                                                                                |  75 kB  00:00:00     
    (19/19): python2-texttable-0.9.1-1.el7.noarch.rpm                                                                                                 |  21 kB  00:00:00     
    -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    总计                                                                                                                                     730 kB/s | 1.2 MB  00:00:01     
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    正在安装    : python-six-1.9.0-2.el7.noarch                                                                                                                       1/19 
    正在安装    : python-websocket-client-0.32.0-116.el7.noarch                                                                                                       2/19 
    正在安装    : python-docker-pycreds-1.10.6-3.el7.noarch                                                                                                           3/19 
    正在安装    : python2-dockerpty-0.4.1-9.el7.noarch                                                                                                                4/19 
    正在安装    : python-urllib3-1.10.2-3.el7.noarch                                                                                                                  5/19 
    正在安装    : python-ipaddress-1.0.16-2.el7.noarch                                                                                                                6/19 
    正在安装    : python-enum34-1.0.4-1.el7.noarch                                                                                                                    7/19 
    正在安装    : python2-docopt-0.6.2-7.el7.noarch                                                                                                                   8/19 
    正在安装    : python-chardet-2.2.1-1.el7_1.noarch                                                                                                                 9/19 
    正在安装    : python-requests-2.6.0-1.el7_1.noarch                                                                                                               10/19 
    正在安装    : python-docker-py-1.10.6-3.el7.noarch                                                                                                               11/19 
    正在安装    : python2-cached_property-1.3.0-7.el7.noarch                                                                                                         12/19 
    正在安装    : libyaml-0.1.4-11.el7_0.x86_64                                                                                                                      13/19 
    正在安装    : PyYAML-3.10-11.el7.x86_64                                                                                                                          14/19 
    正在安装    : python2-texttable-0.9.1-1.el7.noarch                                                                                                               15/19 
    正在安装    : python-repoze-lru-0.4-3.el7.noarch                                                                                                                 16/19 
    正在安装    : python2-jsonschema-2.5.1-3.el7.noarch                                                                                                              17/19 
    正在安装    : python2-backports-functools_lru_cache-1.2.1-4.el7.noarch                                                                                           18/19 
    正在安装    : docker-compose-1.9.0-5.el7.noarch                                                                                                                  19/19 
    验证中      : python2-backports-functools_lru_cache-1.2.1-4.el7.noarch                                                                                            1/19 
    验证中      : python-docker-pycreds-1.10.6-3.el7.noarch                                                                                                           2/19 
    验证中      : python-repoze-lru-0.4-3.el7.noarch                                                                                                                  3/19 
    验证中      : docker-compose-1.9.0-5.el7.noarch                                                                                                                   4/19 
    验证中      : python-requests-2.6.0-1.el7_1.noarch                                                                                                                5/19 
    验证中      : python2-texttable-0.9.1-1.el7.noarch                                                                                                                6/19 
    验证中      : python2-jsonschema-2.5.1-3.el7.noarch                                                                                                               7/19 
    验证中      : libyaml-0.1.4-11.el7_0.x86_64                                                                                                                       8/19 
    验证中      : python2-cached_property-1.3.0-7.el7.noarch                                                                                                          9/19 
    验证中      : python-chardet-2.2.1-1.el7_1.noarch                                                                                                                10/19 
    验证中      : python2-docopt-0.6.2-7.el7.noarch                                                                                                                  11/19 
    验证中      : python-enum34-1.0.4-1.el7.noarch                                                                                                                   12/19 
    验证中      : python-docker-py-1.10.6-3.el7.noarch                                                                                                               13/19 
    验证中      : python-websocket-client-0.32.0-116.el7.noarch                                                                                                      14/19 
    验证中      : PyYAML-3.10-11.el7.x86_64                                                                                                                          15/19 
    验证中      : python2-dockerpty-0.4.1-9.el7.noarch                                                                                                               16/19 
    验证中      : python-ipaddress-1.0.16-2.el7.noarch                                                                                                               17/19 
    验证中      : python-six-1.9.0-2.el7.noarch                                                                                                                      18/19 
    验证中      : python-urllib3-1.10.2-3.el7.noarch                                                                                                                 19/19 

    已安装:
    docker-compose.noarch 0:1.9.0-5.el7                                                                                                                                    

    作为依赖被安装:
    PyYAML.x86_64 0:3.10-11.el7                                     libyaml.x86_64 0:0.1.4-11.el7_0                   python-chardet.noarch 0:2.2.1-1.el7_1               
    python-docker-py.noarch 0:1.10.6-3.el7                          python-docker-pycreds.noarch 0:1.10.6-3.el7       python-enum34.noarch 0:1.0.4-1.el7                  
    python-ipaddress.noarch 0:1.0.16-2.el7                          python-repoze-lru.noarch 0:0.4-3.el7              python-requests.noarch 0:2.6.0-1.el7_1              
    python-six.noarch 0:1.9.0-2.el7                                 python-urllib3.noarch 0:1.10.2-3.el7              python-websocket-client.noarch 0:0.32.0-116.el7     
    python2-backports-functools_lru_cache.noarch 0:1.2.1-4.el7      python2-cached_property.noarch 0:1.3.0-7.el7      python2-dockerpty.noarch 0:0.4.1-9.el7              
    python2-docopt.noarch 0:0.6.2-7.el7                             python2-jsonschema.noarch 0:2.5.1-3.el7           python2-texttable.noarch 0:0.9.1-1.el7              

    完毕！

###### 查看Docker-compose版本

    [mingguilu@localhost ~]$ sudo docker-compose --version
    docker-compose version 1.9.0, build 2585387

---

#### 参考文档：

* 《Install Docker》:  [https://docs.docker.com/install/](https://docs.docker.com/install/)

* 《Get Docker EE for CentOS》:  [https://docs.docker.com/install/linux/docker-ee/centos/](https://docs.docker.com/install/linux/docker-ee/centos/)

* 《Get Docker CE for CentOS》:  [https://docs.docker.com/install/linux/docker-ce/centos/](https://docs.docker.com/install/linux/docker-ce/centos/)

* 《Install Docker Compose》:  [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)

