---
title: Keepalived+Nginx实现主备负载均衡高可用
tags:
  - CentOS
  - nginx
  - keepalived
categories: 运维之道
photos: /images/20180519/20180519_00.jpg
date: 2018-05-19 15:33:42
description:
---

#### 项目简介

![](/images/20180519/20180519_01.png)
 
####  安装nginx

6台服务器上安装nginx方法一样，下面以其中一台为例：

参考nginx官方网站安装方法使用yum安装：[http://nginx.org/en/linux_packages.html#stable](http://nginx.org/en/linux_packages.html#stable)

<!-- more -->

##### 添加yum源

    [root@mg-nginx-03 ~]# vi /etc/yum.repos.d/nginx.repo
    [root@mg-nginx-03 ~]#
    [root@mg-nginx-03 ~]# cat /etc/yum.repos.d/nginx.repo
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/7/$basearch/
    gpgcheck=0
    enabled=1

##### 清理yum缓存

    [root@mg-nginx-03 ~]# yum clean all
    已加载插件：fastestmirror
    正在清理软件源： base epel extras nginx updates
    Cleaning up everything
    Maybe you want: rm -rf /var/cache/yum, to also free up space taken by orphaned data from disabled or removed repos
    Cleaning up list of fastest mirrors

##### 安装nginx

    [root@mg-nginx-03 ~]# yum install -y nginx

    已安装:
      nginx.x86_64 1:1.14.0-1.el7_4.ngx                                        

    完毕！

##### 查看nginx相关文件

    [root@mg-nginx-03 ~]# find / -name "nginx"
    /etc/logrotate.d/nginx
    /etc/sysconfig/nginx
    /etc/nginx                                                            #主配置文件
    /var/lib/yum/repos/x86_64/7/nginx
    /var/log/nginx                                                     #日志
    /var/cache/yum/x86_64/7/nginx
    /var/cache/nginx
    /usr/sbin/nginx                                                    #nginx启动文件
    /usr/lib64/nginx
    /usr/share/nginx                                                  #网站文件存放目录
    /usr/libexec/initscripts/legacy-actions/nginx

##### 管理nginx应用

    [root@mg-nginx-03 conf.d]# systemctl start nginx         #启动nginx
    [root@mg-nginx-03 conf.d]# systemctl enable nginx      #设置nginx开机启动
    Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
    [root@mg-nginx-03 conf.d]# systemctl status nginx        #查看nginx运行状态
    ● nginx.service - nginx - high performance web server
      Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)
      Active: active (running) since 四 2018-05-24 11:14:52 CST; 53s ago
        Docs: http://nginx.org/en/docs/
      Process: 10694 ExecStop=/bin/kill -s TERM $MAINPID (code=exited, status=0/SUCCESS)
      Process: 10709 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
    Main PID: 10710 (nginx)
      CGroup: /system.slice/nginx.service
              ├─10710 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
              └─10711 nginx: worker process

    5月 24 11:14:52 mg-nginx-03 systemd[1]: Starting nginx - high performance web server...
    5月 24 11:14:52 mg-nginx-03 systemd[1]: Started nginx - high performance web server.

    [root@mg-nginx-03 conf.d]# systemctl restart nginx        #重启nginx
    [root@mg-nginx-03 conf.d]# systemctl stop nginx            #停止nginx


#### 配置虚拟主机发布网站

接下来将在四台nginx站点上配置虚拟主机发布我的静态博客网站，为了方便之后检验nginx负载均衡的效果，将每台站点上的博客首页稍做修改，添加上“位于mg-nginx-xx上的blog”，下面以其中一台为例

##### 创建虚拟主机配置文件

nginx默认配置文件定义了虚拟主机配置文件存放在/etc/nginx/conf.d/下，以*.conf结尾

    [root@mg-nginx-03 nginx]# grep "include" /etc/nginx/nginx.conf
        include       /etc/nginx/mime.types;
        include /etc/nginx/conf.d/*.conf;

将缺省的配置文件default.conf拷贝一份重名为blog.conf，并修改配置

    [root@mg-nginx-03 conf.d]# cp -a default.conf blog.conf
    [root@mg-nginx-03 conf.d]#
    [root@mg-nginx-03 conf.d]# cat blog.conf
    server {
        listen       8080;                                        #端口修改为8080
        server_name  localhost;                           #由于实验环境中使用IP:Port访问，此处缺省即可，如规划域名可修改为域名

        #charset koi8-r;
        #access_log  /var/log/nginx/host.access.log  main;

        location / {
            root   /usr/share/nginx/blog;              #网站主目录修改为/usr/share/nginx/blog
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }

##### 将静态博客网站的blog目录上传到/usr/share/nginx下

推荐一个文件上传下载的工具 lrzsz，可通过yum安装之后使用 rz 上传文件，sz filename 下载文件

    [root@mg-nginx-03 nginx]# cd /tmp/
    [root@mg-nginx-03 tmp]# rz   
    [root@mg-nginx-03 tmp]# ls
    blog.tar.gz
    [root@mg-nginx-03 tmp]# tar -zxvf  blog.tar.gz  -C  /usr/share/nginx/
    [root@mg-nginx-03 tmp]# cd /usr/share/nginx/
    [root@mg-nginx-03 nginx]# ll
    总用量 0
    drwxr-xr-x 15 mingguilu mingguilu 253 5月  24 14:09  blog
    drwxr-xr-x  2 root      root       40 5月  24 10:45  html
    [root@mg-nginx-03 nginx]# ll blog/
    总用量 4824
    drwxr-xr-x  3 mingguilu mingguilu      16 5月  22 16:48 2016
    drwxr-xr-x  8 mingguilu mingguilu      66 5月  24 14:00 2017
    drwxr-xr-x  4 mingguilu mingguilu      26 5月  24 14:00 2018
    -rw-r--r--  1 mingguilu mingguilu     710 5月  22 17:26 404.html
    drwxr-xr-x  2 mingguilu mingguilu      24 5月  22 17:25 about
    drwxr-xr-x  6 mingguilu mingguilu      72 5月  24 14:00 archives
    drwxr-xr-x  6 mingguilu mingguilu     104 5月  24 14:00 categories
    -rw-r--r--  1 mingguilu mingguilu      19 5月  22 17:26 CNAME
    drwxr-xr-x  2 mingguilu mingguilu      22 5月  22 16:48 css
    -rw-r--r--  1 mingguilu mingguilu 4590771 5月  22 17:26 debug.log
    drwxr-xr-x 27 mingguilu mingguilu    4096 5月  22 16:48 images
    -rw-r--r--  1 mingguilu mingguilu   75702 5月  24 14:09 index.html
    drwxr-xr-x  3 mingguilu mingguilu      17 5月  22 16:48 js
    drwxr-xr-x 16 mingguilu mingguilu     261 5月  24 14:00 lib
    drwxr-xr-x  4 mingguilu mingguilu      24 5月  24 14:00 page
    -rw-r--r--  1 mingguilu mingguilu  253501 5月  22 17:26 search.xml
    drwxr-xr-x 51 mingguilu mingguilu    4096 5月  24 14:00 tags

##### 将博客首页index.html稍作修改便于检测nginx负载均衡


    <ul id="menu" class="menu">

        <!-- 用于nginx负载均衡测试  start -->
        <li class="menu-item menu-item-home">
          <a href="/" rel="section">
              <i class="menu-item-icon fa fa-fw fa-linux"></i> <br />
              位于mg-nginx-03上的blog
          </a>
        </li>
        <!-- 用于nginx负载均衡测试  end -->

        <li class="menu-item menu-item-home">
          <a href="/" rel="section">
              <i class="menu-item-icon fa fa-fw fa-home"></i> <br />
              首页
          </a>
        </li>

修改后将在首页左上角显示“位于mg-nginx-xx上的blog”，效果如下：

![](/images/20180519/20180519_02.png)

#### 配置nginx负载均衡

接下来在mg-nginx-01上配置负载均衡，注意：只做负载均衡一台nginx即可，后面会引入高可用则需要2台以上nginx做负载转发

Nginx的反向代理和负载均衡，需要用到HttpProxyMoudule和HttpUpstreamModule模块，其中HttpProxyMoudule用来将用户的数据请求转发至其他服务器，HttpUpstreamModule模块用来提供简单的负载均衡技术(轮询、最少连接、客户端IP)，这两个模块在nginx安装时会自动安装上

#####  创建配置文件

    [root@mg-nginx-01 ~]# cd /etc/nginx/conf.d/
    [root@mg-nginx-01 conf.d]# vi blog.conf
    [root@mg-nginx-01 conf.d]# cat blog.conf
    upstream blog {          #upstream定义后端真实服务器集合，blog为服务器组的名称
            #ip_hash;        #根据客户端IP地址的hash值分配固定的后端服务器，否则将使用轮询方式转发数据
            server 172.29.23.45:8080 weight=4 max_fails=2 fail_timeout=30s;
            server 172.29.23.46:8080 weight=4 max_fails=2 fail_timeout=30s;
            server 172.29.23.48:8080 weight=4 max_fails=2 fail_timeout=30s;
            server 172.29.23.49:8080 weight=4 max_fails=2 fail_timeout=30s;
            #weight设置权重值，down指令可以设置某台服务器处于宕机不可用状态，max_fails定义连接后端服务器失败多少次后则认为服务器处理无效状态，fail_timeout定义连接后端服务器超时时间
    }

    server {
        listen       80;     #注意：nginx启动之后，默认使用default.conf配置，80端口会被占用，需要在default.conf中改为其它端口或注释掉不使用
            server_name  blog-uat.mingguilu.com;        #网站域名，如果域名未在DNS注册，需要在客户端hosts文件中添加解析条目：172.29.19.19 blog-uat.mingguilu.com，并清理系统和浏览器缓存
            access_log  /var/log/nginx/blog.log main;   #访问日志

        location / {
            proxy_pass         http://blog;                                #将请求转发给一组服务器
            proxy_set_header   Host             $host;                     #$host代表nginx转发服务器本身
            proxy_set_header   X-Real-IP        $remote_addr;                                       #获取用户真实的IP地址
                  proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;         #获取用户真实的IP地址
            proxy_next_upstream error timeout invalid_header http_404 http_500 http_502 http_504;  #当后端服务器中一台返回error timeout或错误码404、500...时，将请求分配给一下台服务器继续处理
        }
    }

##### 检测nginx配置文件是否合规

    [root@mg-nginx-01 conf.d]# nginx -t
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful

##### 重新加载配置文件


    [root@mg-nginx-01 conf.d]# nginx -s reload

##### 客户端添加本地hosts解析

在C:\Windows\System32\drivers\etc\hosts 文件末尾添加以下内容：

    172.29.19.19  blog-uat.mingguilu.com

刷新系统DNS缓存

    C:\Users\v-mingguilu>ipconfig /flushdns

    Windows IP 配置

    已成功刷新 DNS 解析缓存。

##### 访问测试，如果firefox和chrome未达预期效果，试试IE浏览器

![](/images/20180519/20180519_03.gif)

#### 配置keepalived+nginx高可用

以上完成了4台nginx发布站点和1台nginx负载均衡，如果任意站点宕机，剩余站点可以继续响应客户端访问，但实现负载均衡的这台nginx服务器本身存在风险，如果宕机，将无法通过域名blog-uat.mingguilu.com访问，接下来将扩容一台nginx负载均衡服务器并引入keepalived实现高可用

##### 扩容一台nginx负载均衡服务器

扩容的nginx负载均衡服务器为mg-nginx-02，与上面mg-nginx-01的配置相同

    [root@mg-nginx-02 ~]# cd /etc/nginx/conf.d/
    [root@mg-nginx-02 conf.d]# vi blog.conf
    [root@mg-nginx-02 conf.d]# cat blog.conf
    upstream blog {          #upstream定义后端真实服务器集合，blog为服务器组的名称
            #ip_hash;        #根据客户端IP地址的hash值分配固定的后端服务器，否则将使用轮询方式转发数据
            server 172.29.23.45:8080 weight=4 max_fails=2 fail_timeout=30s;
            server 172.29.23.46:8080 weight=4 max_fails=2 fail_timeout=30s;
            server 172.29.23.48:8080 weight=4 max_fails=2 fail_timeout=30s;
            server 172.29.23.49:8080 weight=4 max_fails=2 fail_timeout=30s;
            #weight设置权重值，down指令可以设置某台服务器处于宕机不可用状态，max_fails定义连接后端服务器失败多少次后则认为服务器处理无效状态，fail_timeout定义连接后端服务器超时时间
    }

    server {
        listen       80;     #注意：nginx启动之后，默认使用default.conf配置，80端口会被占用，需要在default.conf中改为其它端口或注释掉不使用
            server_name  blog-uat.mingguilu.com;        #网站域名
            access_log  /var/log/nginx/blog.log main;   #访问日志

        location / {
            proxy_pass         http://blog;                                #将请求转发给一组服务器
            proxy_set_header   Host             $host;                     #$host代表nginx转发服务器本身
            proxy_set_header   X-Real-IP        $remote_addr;                                       #获取用户真实的IP地址
                  proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;         #获取用户真实的IP地址
            proxy_next_upstream error timeout invalid_header http_404 http_500 http_502 http_504;  #当后端服务器中一台返回error timeout或错误码404、500...时，将请求分配给一下台服务器继续处理
        }
    }

##### 安装keepalived

在mg-nginx-01和mg-nginx-02上安装keepalived，将mg-nginx-01称为主负载均衡服务器，即主Master节点， mg-nginx-02称为备负载均衡服务器，即备Backup节点

安装方法参考官方文档：[http://www.keepalived.org/doc/installing_keepalived.html](http://www.keepalived.org/doc/installing_keepalived.html)

    [root@mg-nginx-01 ~]# yum install -y keepalived

    已安装:
      keepalived.x86_64 0:1.3.5-6.el7                                                                                                                            

    作为依赖被安装:
      lm_sensors-libs.x86_64 0:3.4.0-4.20160601gitf9185e5.el7     net-snmp-agent-libs.x86_64 1:5.7.2-33.el7_5.2     net-snmp-libs.x86_64 1:5.7.2-33.el7_5.2    

    完毕！

##### 查看keepalived部分安装文件

    [root@mg-nginx-01 ~]# find / -name "keepalived*"
    /etc/sysconfig/keepalived
    /etc/selinux/targeted/active/modules/100/keepalived
    /etc/keepalived
    /etc/keepalived/keepalived.conf                        #主配置文件
    /usr/sbin/keepalived                                          #启动程序
    /usr/lib/systemd/system/keepalived.service     
    ......

##### 配置文件说明

配置文件： /etc/keepalived/keepalived.conf

    global_defs {    #全局配置
        notification_email {   定义报警邮件地址
        acassen@firewall.loc
        failover@firewall.loc
        sysadmin@firewall.loc
        }
        notification_email_from Alexandre.Cassen@firewall.loc  #定义发送邮件的地址
        smtp_server 192.168.200.1   #邮箱服务器
        smtp_connect_timeout 30      #定义超时时间
        router_id LVS_DEVEL        #定义路由标识信息，相同局域网唯一
    }
    vrrp_instance VI_1 {           #定义实例
        state MASTER          #状态参数 master/backup 只是说明
        interface eth0        #虚IP地址放置的网卡位置
        virtual_router_id 51  #同一个集群id一致
        priority 100          # 优先级决定是主还是备，越大越优先
        advert_int 1             #主备通讯时间间隔
        authentication {      #认证
            auth_type PASS    
            auth_pass 1111    
        }                        
        virtual_ipaddress {   #设备之间使用的虚拟ip地址
            192.168.200.16    
              192.168.200.17
            192.168.200.18
        }
    }

##### 修改配置文件

###### 主节点配置

    [root@mg-nginx-01 keepalived]# cp -a keepalived.conf keepalived.conf.bak      #养成好习惯，先备份默认配置文件
    [root@mg-nginx-01 keepalived]# vi keepalived.conf
    [root@mg-nginx-01 keepalived]# cat keepalived.conf
    ! Configuration File for keepalived

    global_defs {
      router_id mg-nginx-01                #与备节点的配置不同
    }

    vrrp_instance VI_1 {
        state MASTER                            #与备节点的配置不同
        interface ens33
        virtual_router_id 51
        priority 150                                #与备节点的配置不同
        advert_int 1
        authentication {
            auth_type PASS
            auth_pass 1111
        }
        virtual_ipaddress {
            172.29.19.18/24
        }
    }

###### 备节点配置

    [root@mg-nginx-02 keepalived]# cp -a keepalived.conf keepalived.conf.bak        #养成好习惯，先备份默认配置文件
    [root@mg-nginx-02 keepalived]# vi keepalived.conf
    [root@mg-nginx-02 keepalived]# cat keepalived.conf
    ! Configuration File for keepalived

    global_defs {
      router_id mg-nginx-02                     #与主节点的配置不同
    }

    vrrp_instance VI_1 {
        state BACKUP                                  #与主节点的配置不同
        interface ens33
        virtual_router_id 51
        priority 100                                     #与主节点的配置不同
        advert_int 1
        authentication {
            auth_type PASS
            auth_pass 1111
        }
        virtual_ipaddress {
        172.29.19.18/24
        }
    }

#####  启动keepalived

    [root@mg-nginx-01 keepalived]# systemctl enable keepalived
    Created symlink from /etc/systemd/system/multi-user.target.wants/keepalived.service to /usr/lib/systemd/system/keepalived.service.
    [root@mg-nginx-01 keepalived]#
    [root@mg-nginx-01 keepalived]# systemctl start keepalived


    [root@mg-nginx-02 keepalived]# systemctl enable keepalived
    Created symlink from /etc/systemd/system/multi-user.target.wants/keepalived.service to /usr/lib/systemd/system/keepalived.service.
    [root@mg-nginx-02 keepalived]#
    [root@mg-nginx-02 keepalived]# systemctl start keepalived

##### 查看虚拟ip状态

主节点优先级设置为：priority 150 ，备节点优先级设置为：priority 100

所以当前虚拟ip飘在主节点mg-nginx-01上：

    [root@mg-nginx-01 keepalived]# ip add
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
          valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
          valid_lft forever preferred_lft forever
    2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:bc:f8:34 brd ff:ff:ff:ff:ff:ff
        inet 172.29.19.19/24 brd 172.29.19.255 scope global dynamic ens33
          valid_lft 518994sec preferred_lft 518994sec
        inet 172.29.19.18/24 scope global secondary ens33                                     #keepalived虚拟ip
          valid_lft forever preferred_lft forever
        inet6 fe80::eaa7:1c99:3289:8d13/64 scope link
          valid_lft forever preferred_lft forever

##### 修改客户端本地hosts解析

将C:\Windows\System32\drivers\etc\hosts 文件修改如下：

    172.29.19.18  blog-uat.mingguilu.com

刷新系统DNS缓存，清理浏览器缓存

    C:\Users\v-mingguilu>ipconfig /flushdns

    Windows IP 配置

    已成功刷新 DNS 解析缓存。

##### 访问测试，成功！

![](/images/20180519/20180519_04.gif)

#####  模拟一下主节点发生故障宕机，备节点接管服务

将主节点上的nginx和keepalived都停掉：

    [root@mg-nginx-01 ~]# systemctl stop nginx

    [root@mg-nginx-01 ~]# systemctl stop keepalived

查看网卡信息，虚拟ip果然飘到了备节点网卡上：

    [root@mg-nginx-02 ~]# ip add
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
          valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
          valid_lft forever preferred_lft forever
    2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:3b:15:27 brd ff:ff:ff:ff:ff:ff
        inet 172.29.19.20/24 brd 172.29.19.255 scope global dynamic ens33
          valid_lft 688417sec preferred_lft 688417sec
        inet 172.29.19.18/24 scope global secondary ens33
          valid_lft forever preferred_lft forever
        inet6 fe80::362c:96eb:19ff:bf5e/64 scope link
          valid_lft forever preferred_lft forever

在浏览器访问测试结果也正常，keepalived+nginx高可用配置成功！

##### 模拟一下主节点从故障中恢复，夺回主节点身份

重新启动nginx和keepalived服务，很快就夺回主节点地位

    [root@mg-nginx-01 ~]# systemctl start nginx
    [root@mg-nginx-01 ~]# systemctl start keepalived
    [root@mg-nginx-01 ~]#
    [root@mg-nginx-01 ~]# ip add
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
          valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
          valid_lft forever preferred_lft forever
    2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 00:0c:29:bc:f8:34 brd ff:ff:ff:ff:ff:ff
        inet 172.29.19.19/24 brd 172.29.19.255 scope global dynamic ens33
          valid_lft 687526sec preferred_lft 687526sec
        inet 172.29.19.18/24 scope global secondary ens33
          valid_lft forever preferred_lft forever
        inet6 fe80::eaa7:1c99:3289:8d13/64 scope link
          valid_lft forever preferred_lft forever

####  本次实验中出现的一些问题？！

#####  keepalived配置文件中virtual_ipaddress的写法不同产生的网卡信息显示差异

原本虚拟ip简省的配置应该是：


    virtual_ipaddress {
        172.29.19.18
    }

启动keepalived服务后，查看网卡信息：

虚拟ip掩码为32位，但客户端和其他服务器都能ping通，网站正常访问

![](/images/20180519/20180519_05.png)

随后将虚拟ip配置修改为如下：


    virtual_ipaddress {
        172.29.19.18/24
    }

重启keepalived服务后，查看网卡信息：

虚拟ip掩码为24位，显示信息中增加了secondary字段，服务正常

![](/images/20180519/20180519_06.png)

将虚拟ip配置修改为最详细：


    virtual_ipaddress {
        172.29.19.18/24 dev ens33 label ens33:1
    }

重启keepalived服务后，查看网卡信息：

虚拟ip掩码为24位，显示信息中增加了secondary字段，虚拟接口为1，服务正常

![](/images/20180519/20180519_07.png)

##### 主节点nginx服务stop之后，主节点的身份未变，网站无法访问

我原本的理解是，当主节点上的nginx服务stop之后，无法响应客户端数据请求，备节点就会上位接管客户端请求，但这个理解是错误的，实际上故障中的主节点此时依然是主节点，客户端将无法访问网站。

![](/images/20180519/20180519_08.png)

![](/images/20180519/20180519_09.png)

要理解这个意料之外的结果，需要先研究一下keepalived高可用故障切换转移的工作原理： VRRP (Virtual Router Redundancy Protocol ,虚拟路由器冗余协议）

简言之， 在 Keepalived服务正常工作时，主节点会不断地向备节点发送（多播的方式）心跳消息，用以告诉备节点自己还活看，当主节点发生故障时，就无法发送心跳消息，备节点也就因此无法继续检测到来自主节点的心跳了，于是调用自身的接管程序，接管主节点的 IP资源及服务。而当主节点恢复时，备节点又会释放主节点故障时自身接管的IP资源及服务，恢复到原来的备节点角色。

所以只停止主节点上nginx服务，主备节点之间的心跳消息依然正常通信，备节点不会接管客户端请求，而主节点也无法进行响应客户端请求，最终造成客户端访问失败。

当我们把主节点keepalived服务stop时，或者主节点宕机，物理连接不通时，主备节点无法收发心跳消息，备节点就会成功上位！

---

#### 参考文档

* 《Nginx Documentation》：[http://nginx.org/en/docs/](http://nginx.org/en/docs/)
* 《Keepalived User Guide》：[http://www.keepalived.org/doc/](http://www.keepalived.org/doc/)
* 《 keepalived实现服务高可用》： [https://www.cnblogs.com/clsn/p/8052649.html#auto_id_4](https://www.cnblogs.com/clsn/p/8052649.html#auto_id_4)

