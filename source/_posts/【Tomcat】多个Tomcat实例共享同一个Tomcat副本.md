---
title: 【Tomcat】多个tomcat实例共享同一个tomcat副本
tags:
  - java
  - tomcat
  - jdk
categories: 点滴随笔
photos: /images/photos/tomcat.jpg
date: 2018-10-17 20:40:26
description:
---

近期在项目遇到了多个tomcat实例共享一个tomcat副本，其原理就是在各个实例启动脚本中指定CATALINA_HOME为Tomcat的安装目录，CATALINA_BASE为Tomcat实例的根目录

#### 系统环境

    # cat /etc/redhat-release
    Red Hat Enterprise Linux Server release 6.4 (Santiago)

    # ip add
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        inet6 ::1/128 scope host
          valid_lft forever preferred_lft forever
    2: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 08:00:27:67:af:f4 brd ff:ff:ff:ff:ff:ff
        inet 192.168.56.101/24 brd 192.168.56.255 scope global eth2
        inet6 fe80::a00:27ff:fe67:aff4/64 scope link
          valid_lft forever preferred_lft forever
    3: eth3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 08:00:27:ad:37:47 brd ff:ff:ff:ff:ff:ff
        inet 10.0.3.15/24 brd 10.0.3.255 scope global eth3
        inet6 fe80::a00:27ff:fead:3747/64 scope link
          valid_lft forever preferred_lft forever


<!--more-->

####  安装jdk

    # mkdir /usr/java
    # tar -xzf jdk-8u191-linux-x64.tar.gz -C /usr/java/
    # chown root:root /usr/java/jdk1.8.0_191/
    # ln -s jdk1.8.0_191/ latest
    # ll /usr/java/
    total 4
    drwxr-xr-x. 7 root root 4096 Oct 17 21:39 jdk1.8.0_191
    lrwxrwxrwx. 1 root root   13 Oct 17 21:39 latest -> jdk1.8.0_191/
    # vim /etc/profile.d/jdk.sh
    # cat /etc/profile.d/jdk.sh
    JAVA_HOME=/usr/java/latest
    JRE_HOME=/usr/java/latest/jre
    CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
    PATH=$JAVA_HOME/bin:$PATH
    export JAVA_HOME JRE_HOME CLASSPATH PATH
    # chmod +x /etc/profile.d/jdk.sh
    # source /etc/profile
    # java -version
    java version "1.8.0_191"
    Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
    Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)


#### 安装Tomcat

    # tar -xzf apache-tomcat-7.0.73.tar.gz -C /opt/
    # cd /opt/
    # ln -s apache-tomcat-7.0.73/ tomcat-latest
    # ll
    total 8
    drwxr-xr-x. 2 root root 4096 Oct 17 21:48 apache-tomcat-7.0.73
    lrwxrwxrwx. 1 root root   21 Oct 17 21:51 tomcat-latest -> apache-tomcat-7.0.73/


#### 启动Tomcat

    # sh startup.sh
    Using CATALINA_BASE:   /opt/tomcat-latest
    Using CATALINA_HOME:   /opt/tomcat-latest
    Using CATALINA_TMPDIR: /opt/tomcat-latest/temp
    Using JRE_HOME:        /usr/java/latest/jre
    Using CLASSPATH:       /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar
    Tomcat started.

    # ps -ef|grep tomcat
    root      3891     1  6 21:57 pts/1    00:00:03 /usr/java/latest/jre/bin/java -Djava.util.logging.config.file=/opt/tomcat-latest/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/opt/tomcat-latest/endorsed -classpath /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar -Dcatalina.base=/opt/tomcat-latest -Dcatalina.home=/opt/tomcat-latest -Djava.io.tmpdir=/opt/tomcat-latest/temp org.apache.catalina.startup.Bootstrap start
    root      3911  3550  0 21:58 pts/1    00:00:00 grep tomcat

    # netstat -lnp |grep 3891
    tcp        0      0 :::8080                     :::*                        LISTEN      3891/java
    tcp        0      0 ::ffff:127.0.0.1:8005       :::*                        LISTEN      3891/java
    tcp        0      0 :::8009                     :::*                        LISTEN      3891/java

    # iptables -I INPUT -p tcp --dport 8080 -j ACCEPT


在浏览器访问tomcat: http://192.168.56.101:8080

![](/images/20181017/20181017_01.png)

#### 创建多个Tomcat实例

创建三个目录用于发布3个Tomcat实例，并将/opt/tomcat-latest下的conf,logs,temp,webapps,work目录拷贝到三个目录下

    # mkdir -p /var/apphome/{app1_18080,app2_28080,app3_38080}
    # ll /var/apphome/
    drwxr-xr-x. 2 root root 4096 Oct 17 22:37 app1_18080
    drwxr-xr-x. 2 root root 4096 Oct 17 22:37 app2_28080
    drwxr-xr-x. 2 root root 4096 Oct 17 22:37 app3_38080
    # cp -rf /opt/tomcat-latest/{conf,logs,temp,webapps,work} /var/apphome/app1_18080/
    # cp -rf /opt/tomcat-latest/{conf,logs,temp,webapps,work} /var/apphome/app2_28080/
    # cp -rf /opt/tomcat-latest/{conf,logs,temp,webapps,work} /var/apphome/app3_38080/

#### 修改配置文件

修改app1_18080,app2_28080,app3_38080三个实例下的 conf/server.xml 文件中的端口配置

- Server port="8005"  监听关闭tomcat的请求
- Connector port="8080   监听来自客户端的请求
- redirectPort="8443"  指定正在处理http请求时收到了一个SSL传输请求后重定向的端口号

    <Server port="8005" shutdown="SHUTDOWN">

    <Connector port="8080" protocol="HTTP/1.1"
                  connectionTimeout="20000"
                  redirectPort="8443" />

实例app1_18080

    <Server port="18005" shutdown="SHUTDOWN">

    <Connector port="18080" protocol="HTTP/1.1"
                  connectionTimeout="20000"
                  redirectPort="18443" />

实例app2_28080

    <Server port="28005" shutdown="SHUTDOWN">

    <Connector port="28080" protocol="HTTP/1.1"
                  connectionTimeout="20000"
                  redirectPort="28443" />

实例app3_38080

    <Server port="38005" shutdown="SHUTDOWN">

    <Connector port="38080" protocol="HTTP/1.1"
                  connectionTimeout="20000"
                  redirectPort="38443" />

#### 创建启动脚本

实例app1_18080

    # cat /var/apphome/app1_18080/startup.sh
   
    #!/bin/bash
    export CATALINA_HOME=/opt/tomcat-latest
    export CATALINA_BASE=/var/apphome/app1_18080
    tomcatd=${CATALINA_HOME}/bin/catalina.sh
    $tomcatd start

实例app2_28080

    # cat /var/apphome/app2_28080/startup.sh

    #!/bin/bash
    export CATALINA_HOME=/opt/tomcat-latest
    export CATALINA_BASE=/var/apphome/app2_28080
    tomcatd=${CATALINA_HOME}/bin/catalina.sh
    $tomcatd start

实例app3_38080

    # cat /var/apphome/app3_38080/startup.sh
    
    #!/bin/bash
    export CATALINA_HOME=/opt/tomcat-latest
    export CATALINA_BASE=/var/apphome/app3_38080
    tomcatd=${CATALINA_HOME}/bin/catalina.sh
    $tomcatd start

#### 修改index.jsp页面便于验证

实例app1_18080

    # vim /var/apphome/app1_18080/webapps/ROOT/index.jsp
    <div id="asf-box">
        <h1>${pageContext.servletContext.serverInfo}</h1>
        <h1>from app1_18080</h1>
    </div>


实例app2_28080

    # vim /var/apphome/app2_28080/webapps/ROOT/index.jsp
    <div id="asf-box">
        <h1>${pageContext.servletContext.serverInfo}</h1>
        <h1>from app2_28080</h1>
    </div>

实例app3_38080

    # vim /var/apphome/app3_38080/webapps/ROOT/index.jsp
    <div id="asf-box">
        <h1>${pageContext.servletContext.serverInfo}</h1>
        <h1>from app3_38080</h1>
    </div>

####  启动Tomcat实例

实例app1_18080

    # sh /var/apphome/app1_18080/startup.sh
    Using CATALINA_BASE:   /var/apphome/app1_18080
    Using CATALINA_HOME:   /opt/tomcat-latest
    Using CATALINA_TMPDIR: /var/apphome/app1_18080/temp
    Using JRE_HOME:        /usr/java/latest/jre
    Using CLASSPATH:       /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar
    Tomcat started.

实例app2_28080

    # sh /var/apphome/app2_28080/startup.sh
    Using CATALINA_BASE:   /var/apphome/app2_28080
    Using CATALINA_HOME:   /opt/tomcat-latest
    Using CATALINA_TMPDIR: /var/apphome/app2_28080/temp
    Using JRE_HOME:        /usr/java/latest/jre
    Using CLASSPATH:       /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar
    Tomcat started.

实例app3_38080

    # sh /var/apphome/app3_38080/startup.sh
    Using CATALINA_BASE:   /var/apphome/app3_38080
    Using CATALINA_HOME:   /opt/tomcat-latest
    Using CATALINA_TMPDIR: /var/apphome/app3_38080/temp
    Using JRE_HOME:        /usr/java/latest/jre
    Using CLASSPATH:       /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar
    Tomcat started.

#### 查看进程和端口占用

    # ps -ef|grep java
    root      4483     1  0 23:27 pts/1    00:00:07 /usr/java/latest/jre/bin/java -Djava.util.logging.config.file=/opt/tomcat-latest/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/opt/tomcat-latest/endorsed -classpath /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar -Dcatalina.base=/opt/tomcat-latest -Dcatalina.home=/opt/tomcat-latest -Djava.io.tmpdir=/opt/tomcat-latest/temp org.apache.catalina.startup.Bootstrap start
    root      4754     1  2 23:50 pts/1    00:00:06 /usr/java/latest/jre/bin/java -Djava.util.logging.config.file=/var/apphome/app1_18080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/opt/tomcat-latest/endorsed -classpath /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar -Dcatalina.base=/var/apphome/app1_18080 -Dcatalina.home=/opt/tomcat-latest -Djava.io.tmpdir=/var/apphome/app1_18080/temp org.apache.catalina.startup.Bootstrap start
    root      4795     1  1 23:51 pts/1    00:00:03 /usr/java/latest/jre/bin/java -Djava.util.logging.config.file=/var/apphome/app2_28080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/opt/tomcat-latest/endorsed -classpath /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar -Dcatalina.base=/var/apphome/app2_28080 -Dcatalina.home=/opt/tomcat-latest -Djava.io.tmpdir=/var/apphome/app2_28080/temp org.apache.catalina.startup.Bootstrap start
    root      4859     1 54 23:54 pts/1    00:00:03 /usr/java/latest/jre/bin/java -Djava.util.logging.config.file=/var/apphome/app3_38080/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.endorsed.dirs=/opt/tomcat-latest/endorsed -classpath /opt/tomcat-latest/bin/bootstrap.jar:/opt/tomcat-latest/bin/tomcat-juli.jar -Dcatalina.base=/var/apphome/app3_38080 -Dcatalina.home=/opt/tomcat-latest -Djava.io.tmpdir=/var/apphome/app3_38080/temp org.apache.catalina.startup.Bootstrap start
    root      4876  3550  0 23:54 pts/1    00:00:00 grep java

    # netstat -lnp |grep java
    tcp        0      0 :::28080                    :::*                        LISTEN      4795/java
    tcp        0      0 :::8080                     :::*                        LISTEN      4483/java
    tcp        0      0 ::ffff:127.0.0.1:38005      :::*                        LISTEN      4859/java
    tcp        0      0 ::ffff:127.0.0.1:18005      :::*                        LISTEN      4754/java
    tcp        0      0 :::38080                    :::*                        LISTEN      4859/java
    tcp        0      0 :::18080                    :::*                        LISTEN      4754/java
    tcp        0      0 ::ffff:127.0.0.1:28005      :::*                        LISTEN      4795/java
    tcp        0      0 ::ffff:127.0.0.1:8005       :::*                        LISTEN      4483/java
    tcp        0      0 :::8009       

#### 访问测试

http://192.168.56.101:8080

![](/images/20181017/20181017_01.png)

http://192.168.56.101:18080/

![](/images/20181017/20181017_02.png)

http://192.168.56.101:28080/

![](/images/20181017/20181017_03.png)

http://192.168.56.101:38080/

![](/images/20181017/20181017_04.png)


#### 查看日志

日志的路径和名称定义在 conf/logging.properties和server.xml，下面以实例app3_38080来演示

    # tail -fn 500 /var/apphome/app3_38080/logs/localhost_access_log.2018-10-18.txt
    192.168.56.1 - - [18/Oct/2018:00:03:58 +0800] "GET /favicon.ico HTTP/1.1" 200 21630
    192.168.56.1 - - [18/Oct/2018:00:04:01 +0800] "GET /examples/ HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:04:01 +0800] "GET /favicon.ico HTTP/1.1" 200 21630
    192.168.56.1 - - [18/Oct/2018:00:04:19 +0800] "GET /docs/config/ HTTP/1.1" 200 10964
    192.168.56.1 - - [18/Oct/2018:00:04:19 +0800] "GET /docs/images/asf-logo.gif HTTP/1.1" 200 7279
    192.168.56.1 - - [18/Oct/2018:00:04:19 +0800] "GET /docs/images/tomcat.gif HTTP/1.1" 200 2066
    192.168.56.1 - - [18/Oct/2018:00:04:21 +0800] "GET /docs/config/server.html HTTP/1.1" 200 13094
    192.168.56.1 - - [18/Oct/2018:00:04:36 +0800] "GET /docs/config/executor.html HTTP/1.1" 200 14261
    192.168.56.1 - - [18/Oct/2018:00:23:04 +0800] "GET /docs/config/server.html HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:23:04 +0800] "GET /docs/images/tomcat.gif HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:23:04 +0800] "GET /docs/images/asf-logo.gif HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:23:18 +0800] "GET /docs/config/ HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:24:26 +0800] "GET /docs/config/http.html HTTP/1.1" 200 90399
    192.168.56.1 - - [18/Oct/2018:00:48:02 +0800] "GET / HTTP/1.1" 200 11258
    192.168.56.1 - - [18/Oct/2018:00:48:02 +0800] "GET /tomcat.png HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:48:02 +0800] "GET /tomcat.css HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:48:02 +0800] "GET /bg-middle.png HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:48:02 +0800] "GET /bg-upper.png HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:48:02 +0800] "GET /bg-button.png HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:48:02 +0800] "GET /asf-logo.png HTTP/1.1" 304 -
    192.168.56.1 - - [18/Oct/2018:00:48:02 +0800] "GET /bg-nav.png HTTP/1.1" 304 -


---


