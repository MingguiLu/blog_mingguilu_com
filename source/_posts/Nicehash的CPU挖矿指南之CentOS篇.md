---
title: Nicehash的CPU挖矿指南之CentOS篇
tags:
  - nicehash
  - bitcoin
  - cpuminer
  - 挖矿
categories: 趣玩尝新
photos: /images/photos/nicehash.png
date: 2018-04-1 16:27:37
description:
---

时至今日，随着挖矿难度提升，加密货币价格一再腰斩，个人挖矿只是浪费精力和资源而已，完全不推荐，本篇文章仅作为体验教程

#### Nicehash矿池相比国内外XMR矿池

Nicehash矿池相比于其他国内外的XMR矿池，有一定的优势，或网络连通性好，或矿工多算力强，容易爆块获得收益，最重要的是结算周期短，相对国内矿池1.5～3XMR的高门槛，Nicehash结算到交易所钱包最低0.0021BTC即可，抽水0.2%也相对较低

#### Linux版本相比Windows版本优缺点

无论Windows还是Linux版本CPU挖矿实际是调用xmr-stak、cpuminer等挖XMR，再抽掉手续费换算成Bitcoin结算给矿工

优点：

* 系统资源开销更低，挖矿软件运行更稳定
* Linux系统便于结合自动化工具进行批量部署和管理
* Linux系统便于持续部署的新版本挖矿软件和算法
* 用户可自定义用于挖矿的CPU线程数

缺点：

* Nichash控制面板无法显示每台矿机的名称，无论实际有多少台矿机，矿机数量只显示1台
* Github上有很多版本的挖矿软件，但有些版本可能会抽水，注意分辨

<!--more-->

#### 注册Nicehash

[https://www.nicehash.com/?refby=1358932&lang=zh](https://www.nicehash.com/?refby=1358932&lang=zh)

#### 推荐系统：CentOS 7 以上

    cat /etc/redhat-release 

    CentOS Linux release 7.4.1708 (Core)

#### 切换到root用户

    sudo su - root

#### 关闭防火墙

    sudo systemctl stop firewalld
    sudo systemctl disable firewalld
    sudo setenforce 0
    sudo sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config

#### 系统调优参数，防止出现内存错误

    echo '* soft memlock 262144' >> /etc/security/limits.conf
    echo '* hard memlock 262144' >> /etc/security/limits.conf
    sysctl -w vm.nr_hugepages=128

#### 安装依赖软件

    yum install -y centos-release-scl epel-release
    yum install -y devtoolset-4-gcc* hwloc-devel libmicrohttpd-devel openssl-devel libcurl-devel libtool automake make git
    yum groupinstall -y "Development Tools"
    scl enable devtoolset-4 bash

#### 创建cpuminer-opt工作目录

    mkdir /data
    cd /data

#### 下载编译cpuminer-opt

Nicehash官方推荐的cpuminer-multi的Github仓库地址为：[https://github.com/tpruvot/cpuminer-multi.git](https://github.com/tpruvot/cpuminer-multi.git)

不过这个版本不支持cryptonightv7算法（或许支持但我不知道怎么用），所以选择以下支持cryptonightv7算法的cpuminer-opt

    git clone https://github.com/JayDDee/cpuminer-opt.git
    cd cpuminer-opt
    ./build.sh

#### 创建日志文件

    mkdir /data/cpuminer-opt/logs
    touch /data/cpuminer-opt/logs/cpuminer.log
    chmod -R 755 /data/cpuminer-opt/logs/cpuminer.log

#### 配置对应算法的挖矿参数

参数简介：

"url" : Stratum服务器地址，格式为：stratum+tcp://算法.位置.nicehash.com:端口，参考[https://www.nicehash.com/farm-mining](https://www.nicehash.com/farm-mining)
"user" : 你的Nicehash钱包地址，或者Nicehash的邮箱账户
"pass" : 密码，默认‘x'，可以空着’ ‘，或者填‘矿工名称:邮箱地址’
"algo" : 挖矿算法，需要跟url中的算法一样，参考[https://www.nicehash.com/algorithm](https://www.nicehash.com/algorithm)
"threads" : 用于挖矿的cpu线程，默认0为所有线程
其他参数： 默认即可

    cp cpuminer-conf.json cpuminer-conf.json.cryptonightv7
    cp cpuminer-conf.json cpuminer-conf.json.lyra2rev2

    vi cpuminer-conf.json.cryptonightv7
    {
            "_comment1" : "Any long-format command line argument ",
            "_comment2" : "may be used in this JSON configuration file",

            "api-bind" : "127.0.0.1:4048",

            "url" : "stratum+tcp://cryptonightv7.hk.nicehash.com:3363",
            "user" : "3PZxBftQ8kt8uUszqM9tFnqwJMKWPpg8zm",
            "pass" : "x",

            "algo" : "cryptonightv7",
            "threads" : 7,
            "cpu-priority" : 0,
            "cpu-affinity" : -1,

            "benchmark" : false,
            "debug" : false,
            "protocol": false,
            "quiet" : false
    }

    vi cpuminer-conf.json.lyra2rev2
    {
            "_comment1" : "Any long-format command line argument ",
            "_comment2" : "may be used in this JSON configuration file",

            "api-bind" : "127.0.0.1:4048",

            "url" : "stratum+tcp://lyra2rev2.hk.nicehash.com:3347",
            "user" : "3PZxBftQ8kt8uUszqM9tFnqwJMKWPpg8zm",
            "pass" : "x",

            "algo" : "lyra2rev2",
            "threads" : 7,
            "cpu-priority" : 0,
            "cpu-affinity" : -1,

            "benchmark" : false,
            "debug" : false,
            "protocol": false,
            "quiet" : false
    }

#### 创建cpuminer对应算法的启动脚本

    mkdir /data/cpuminer-opt/bin
    cd /data/cpuminer-opt/bin

    vi startup-cryptonightv7.sh

    #!/bin/bash
    cd /data/cpuminer-opt
    ./cpuminer -c cpuminer-conf.json.cryptonightv7 >/data/cpuminer-opt/logs/cpuminer.log 2>&1 &

    vi startup-lyra2rev2.sh
    
    #!/bin/bash
    cd /data/cpuminer-opt
    ./cpuminer -c cpuminer-conf.json.lyra2rev2 >/data/cpuminer-opt/logs/cpuminer.log 2>&1 &

    chmod 755 startup-cryptonightv7.sh
    chmod 755 startup-lyra2rev2.sh

#### 创建cpuminer停止脚本

    vi shutdown.sh

    #!/bin/bash
    XMR_STAK_PID=`ps -ef|grep cpuminer|grep -v grep|awk '{print $2}'`
    kill -9 $XMR_STAK_PID
    chmod 755 shutdown.sh

#### 启动cpuminer开始挖矿

    sh startup-cryptonightv7.sh

切换算法挖矿

    sh shutdown.sh
    sh startup-lyra2rev2.sh

#### 实时追踪cpuminer日志

    tail -fn 100 /data/cpuminer-opt/logs/cpuminer.log

#### 实时查看挖矿信息

登录Nicehash，依次访问控制面板 ——> 查看统计数据，即可查看矿机实时挖矿状态

免登陆情况下直接访问：https://www.nicehash.com/miner/你的Nicehash钱包地址

#### 结算到交易所换成RMB

推荐将Nicehash赚取的Bitcoin结算到火币交易所[https://www.huobi.br.com/zh-cn/topic/invited/?invite_code=vg333](https://www.huobi.br.com/zh-cn/topic/invited/?invite_code=vg333)，通过站内法币交易简单方便的将数字货币出售为RMB，支持微信、支付宝收款

![](/images/reading/huobi1.jpg)

---

####  参考文档

* 注册Nicehash：[https://www.nicehash.com/?refby=1358932&lang=zh](https://www.nicehash.com/?refby=1358932&lang=zh)

* 火币交易所：[https://www.huobi.br.com/zh-cn/topic/invited/?invite_code=vg333](https://www.huobi.br.com/zh-cn/topic/invited/?invite_code=vg333)

* 《CPU挖矿指南》： [https://www.nicehash.com/help/cpu-mining](https://www.nicehash.com/help/cpu-mining)

* 《Nicehash算法》： [https://www.nicehash.com/algorithm](https://www.nicehash.com/algorithm)

* 《Nicehash选择服务器》： [https://www.nicehash.com/farm-mining](https://www.nicehash.com/farm-mining)

* 《cpuminer-opt/RELEASE_NOTES》 ：[https://github.com/JayDDee/cpuminer-opt/blob/master/RELEASE_NOTES](https://github.com/JayDDee/cpuminer-opt/blob/master/RELEASE_NOTES)

* 《This is the home of cpuminer-opt, The optimized CPU miner》： [https://bitcointalk.org/index.php?topic=1326803.0](https://bitcointalk.org/index.php?topic=1326803.0)

