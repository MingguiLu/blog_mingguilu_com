---
title: Nicehash的CPU挖矿指南之Windows篇
tags:
  - nicehash
  - bitcoin
  - cpuminer
  - 挖矿
categories: 趣玩尝新
photos: /images/photos/nicehash.png
date: 2018-04-1 8:27:37
description:
---

时至今日，随着挖矿难度提升，加密货币价格一再腰斩，个人挖矿只是浪费精力和资源而已，完全不推荐，本篇文章仅作为体验教程

#### Nicehash矿池相比国内外XMR矿池

Nicehash矿池相比于其他国内外的XMR矿池，有一定的优势，或网络连通性好，或矿工多算力强，容易爆块获得收益，最重要的是结算周期短，相对国内矿池1.5～3XMR的高门槛，Nicehash结算到交易所钱包最低0.0021BTC即可，抽水0.2%也相对较低

#### Windows版本相比Linux版本优缺点

无论Windows还是Linux版本CPU挖矿实际是调ii用xmr-stak、cpuminer等挖XMR，再抽掉手续费换算成Bitcoin结算给矿工

优点：

* 一般个人或办公电脑都是Windows系统
* 下载、安装和配置步骤简单
* GUI界面直观易上手
* 在Nicehash查看挖矿状态时显示矿机名称和总数
* 自动切换最优的算法和矿池

缺点：

* 软件需逐台安装和更新，自动批量管理不容易实现
* 无法自定义用于挖矿的CPU线程数，默认50%

<!--more-->

#### 注册Nicehash

访问nicehash官网，注册一个账号 

[https://www.nicehash.com/?refby=1358932&lang=zh](https://www.nicehash.com/?refby=1358932&lang=zh)

![](/images/20180401/20180401_03_01.jpg)

#### 登录Nicehash，查看BTC钱包地址

![](/images/20180401/20180401_03_02.jpg)

#### 下载[Nicehash](https://miner.nicehash.com/)

[Nicehash Miner](https://miner.nicehash.com/) 支持CPU和GPU挖矿，可以根据电脑的显卡平台选择对应的版本，如果没有独显或独显型号偏低端，只用CPU挖矿的话，随意选择即可

![](/images/20180401/20180401_03_03.jpg)

##### Nicehash Miner for NVIDIA

###### 安装

![](/images/20180401/20180401_03_04.jpg)

![](/images/20180401/20180401_03_05.jpg)

![](/images/20180401/20180401_03_06.jpg)

###### 初始化

![](/images/20180401/20180401_03_07.jpg)

![](/images/20180401/20180401_03_08.jpg)

![](/images/20180401/20180401_03_09.jpg)

由于电脑显卡是ADM的，NVIDIA版本的挖矿软件发出警告不支持AMD GPUs，忽略即可

![](/images/20180401/20180401_03_10.jpg)

Nicehash Miner主界面如下

![](/images/20180401/20180401_03_11.jpg)

###### 参数配置

点击“WALLET”，输入Nicehash邮箱账户，或者BTC钱包地址，我这里使用BTC钱包地址

![](/images/20180401/20180401_03_12.jpg)

![](/images/20180401/20180401_03_13.jpg)

点击“WORKER”,设置这台矿机的名称，挖矿时可以在控制面板追踪这台矿机的信息

![](/images/20180401/20180401_03_15.jpg)

点击“CONFIGURE”,建议保持勾选“Automatic Service Location”，软件将自动连接最优的矿池

![](/images/20180401/20180401_03_16.jpg)

勾选上“Mining”下的三个选项，依次为：系统空闲时自动挖矿、开机启动挖矿、自动控制显卡风扇速度

![](/images/20180401/20180401_03_17.jpg)

###### 启动挖矿

参数配置完成后，点击”START“，开始基准算法的测试

![](/images/20180401/20180401_03_18.jpg)

挖矿进行中

![](/images/20180401/20180401_03_19.jpg)


##### NiceHashMinerLegacy for AMD

###### 下载安装NiceHashMinerLegacy

在Nicehash下载页面点击”DOWNLOAD FOR AMD“后，会跳转到github下载最新版：[https://github.com/nicehash/NiceHashMinerLegacy/releases](https://github.com/nicehash/NiceHashMinerLegacy/releases)

下载完成后直接解压缩即可使用，在解压目录中找到 NiceHashMinerLegacy.exe ，双击运行

![](/images/20180401/20180401_03_20.jpg)

###### 初始化

同意服务协议，选择语言，我这里默认英语，也可下来选择简体中文

![](/images/20180401/20180401_03_21.jpg)

继续点击同意

![](/images/20180401/20180401_03_22.jpg)

挖矿软件检测并提示我的AMD显卡驱动版本过低，建议更新驱动

![](/images/20180401/20180401_03_23.jpg)

等待软件初始化

![](/images/20180401/20180401_03_24.jpg)

Nicehash Miner Legacy主界面

![](/images/20180401/20180401_03_25.jpg)

##### 参数设置

Nicehash Miner Legacy不支持自动寻找最优矿池，需要手动设置，我们选择离自己最近的位置即可

输入Nicehash BTC钱包地址和矿机名称

![](/images/20180401/20180401_03_26.jpg)

点击”settings“，勾选需要的选项：系统空闲时自动挖矿、开机启动挖矿

![](/images/20180401/20180401_03_27.jpg)

##### 启动挖矿

勾选上可用于挖矿的设备，点击’Start‘,弹出是否进行算法基准测试，建议点击’Yes‘，点击’No‘跳过测试直接开始挖矿

![](/images/20180401/20180401_03_28.jpg)

测试时可勾选上’Start mining after benchmark‘，测试完成后自动开始挖矿

![](/images/20180401/20180401_03_29.jpg)

挖矿进行中

![](/images/20180401/20180401_03_30.jpg)

#### 实时查看挖矿信息

登录Nicehash，依次访问控制面板 ——> 查看统计数据，即可查看矿机实时挖矿状态

免登陆情况下直接访问：https://www.nicehash.com/miner/你的Nicehash钱包地址

#### 结算和提现

Nicehash每天下午6点左右支付到BTC钱包，但未支付的余额需大于0.001BTC

当BTC钱包余额大于0.2BTC，即可提现到任一比特币钱包，比如提到交易所兑现

![](/images/20180401/20180401_03_32.jpg)

推荐将Nicehash赚取的Bitcoin结算到火币交易所[https://www.huobi.br.com/zh-cn/topic/invited/?invite_code=vg333](https://www.huobi.br.com/zh-cn/topic/invited/?invite_code=vg333)，通过站内法币交易简单方便的将数字货币出售为RMB，支持微信、支付宝收款

![](/images/reading/huobi1.jpg)

提现步骤如下：

登录Nicehash，菜单栏依次点击”钱包“——>"提现"——>”Withdraw to my BTC wallet“

![](/images/20180401/20180401_03_33.jpg)

输入要转入的BTC钱包地址和数额，点击”添加到提现“

![](/images/20180401/20180401_03_34.jpg)

然后在提现列表中点击”确认“该笔提现，提现时间一般要8小时左右

---

#### 参考文档

* 注册Nicehash：[https://www.nicehash.com/?refby=1358932&lang=zh](https://www.nicehash.com/?refby=1358932&lang=zh)

*  火币交易所：[https://www.huobi.br.com/zh-cn/topic/invited/?invite_code=vg333](https://www.huobi.br.com/zh-cn/topic/invited/?invite_code=vg333)

* 收益能力计算器:[https://www.nicehash.com/profitability-calculator](https://www.nicehash.com/profitability-calculator)

* 收益 & 款项支付 :[https://www.nicehash.com/help/when-and-how-do-you-get-paid](https://www.nicehash.com/help/when-and-how-do-you-get-paid)

* 服务 & 费用：[https://www.nicehash.com/help/fees](https://www.nicehash.com/help/fees)