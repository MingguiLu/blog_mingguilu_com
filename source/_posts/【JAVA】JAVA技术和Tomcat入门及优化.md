---
title: 【JAVA】JAVA技术和Tomcat入门及优化
tags:
  - java
  - jdk
  - tomcat
  - 马哥
categories: 运维之道
photos: /images/photos/java.jpg
date: 2018-09-25 23:38:42
description:
---

#### MVC框架讲解

* 模型Model 处理应用程序数据逻辑
* 视图View 处理数据显示
* 控制器Controller 处理用户交互

#### 平台移植

##### c/c++

c 面向过程，c++ 面向对象，都与硬件和操作系统关系紧密

* 缺点：跨平台性差、移植困难、维护成本高
* 优点：运行速度快、适合开发驱动程序

c: API(Application programming Interface)
    OS，System call：
        API:

Windows，API
Linux，API
    POSIX: Protable Oprating System

ABI： Application Binary Interface

<!--more-->

#### JAVA

包含四个独立却又彼此相关的技术：

* Java程序设计语言
* Java API
* Java class文件格式
* JVM: Java virtual Machine

#### JVM和Java库

Once for all (Write once,Run anywhere)一次编译，到处运行

.java ——> .class(bytecode)

![](/images/20180925/20180925_01.png)

#### JVM实现方式

* 一次性解释器，解释字节码并执行
* 即时编译器(just-in-time complier),依赖于更多内存缓存解释后的结果
* 自适应编辑器，缓存20%左右代码，提高80%的速度

#### jdk讲解

##### Sun：hotspot JVM（1999年面世）

* JRE=JVM+Java SE API, Java运行时环境，只能运行编译好的Java程序
* JDK=Java+API+JVM，Java开发(编译)+运行环境，是用于实现Java程序开发的最小环境

##### OpenJDK

开发(编译)+运行


#### Java应用领域分类

*  JAVA SE: 标准版Standard Edition <—— J2SE
*  JAVA EE: 企业版Enterprise Edition <—— j2EE
*  JAVA ME: 移动版Mobile Edition <—— J2ME，未广泛应用

1995年，JAVA 1.0面世，作者James Gosling，项目名Green Project

1999年，hotspot JVM面世

2006年，Sun在GPL协议下开源Java

2009年，Oracle以74亿美元收购Sun，包括Java

#### Servlet讲解

CGI：common gateway Interface ，即通用网关借口，一种协议或规范，在用户访问某种特定资源的时候触发web server调用外部程序执行请求

Web服务器额外调用其他程序来执行动态资源请求： web server ——>  mime ——> html ——> 返回请求数据

![](/images/20180925/20180925_02.png)

Servlet： 以Java语言实现CGI协议，增加对http协议的处理能力

#### JSP概念

JSP: Java Server Page，是Servlet的一种特殊类，在Servle基础上升级改造的能够实现嵌入html文档中的一种网页开发技术

jsp(.jsp) ——jasper——> Servlet(.java) ——JVM——> .class  

jsp与php除了是不同语言的不同技术之外，jsp性能优于php，适合大型web项目

#### Java技术

Servlet container: Servlet容器
Web Container：Web容器

![](/images/20180925/20180925_03.png)

#### Java运行内部机制

线程私有内存区：程序计数器、Java虚拟机栈
线程共享内存区：方法区、堆

#### Java堆栈介绍

![](/images/20180925/20180925_04.png)

堆：存储大量对象实例，容易造成内存溢出，需要自动内存回收机制

#### Java垃圾回收机制

GC: Garbage Collection，垃圾回收器

垃圾回收算法：

* Mark-Sweep 标记 ——> 清除：容易产生碎片
* Copying 复制：不会产生碎片，但占用更多空间
* Mark-Compact 标记 ——> 整理：不产生碎片，同时更节省空间

垃圾回收器：

* Serial： 单线程回收，一次收回一个
* ParNew： 多线程收回，一次回收多个，依赖多个CPU
* Parallel Scavenge： 降低垃圾回收时间比例
* Serial Old：  单线程回收，Serial老年代版本
* Parallel Old：  多线程回收，Parallel老年代版本
* CMS: Concurrent Mark Sweep ，并发收集、低停顿，但无法收集浮动垃圾，基于标记——>清除会产生碎片
* G1：多线程大容量回收，高吞吐量同时，减少中断比例

#### 企业级JDK应用

* sun： JRE、JDK
* OpenSource：  OpenJDK

#### JDK安装

[Oracle JAVA SE 下载地址](https://www.oracle.com/technetwork/java/javase/archive-139210.html)

* rpm
* 通用二进制
* 源码编译

##### rpm包




##### 

#### 参考文档


