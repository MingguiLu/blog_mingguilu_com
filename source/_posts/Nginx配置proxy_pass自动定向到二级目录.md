---
title: Nginx配置proxy_pass自动定向到二级目录
tags:
  - CentOS
  - nginx
  - proxy_pass
categories: 运维之道
photos: /images/20180520/20180520_00.gif
date: 2018-05-20 12:49:21
description:
---

近期在生产中遇到一个需求：

某部门CRM系统运行在一台CentOS服务器(假设IP为 172.29.23.48)上，该系统使用一个java web的war包启动，客户端使用IP地址加端口接二级目录来访问： http://172.29.23.48:8088/crm 。现在需要再扩容1台CRM服务器，加入Keepalived高可用方案，并绑定域名(假设为 crm.mingguilu.com)进行访问：http://crm.mingguilu.com

在扩容完成并配置Keepalived+Nginx负载均衡高可用后，仍然需要域名接二级目录访问： http://crm.mingguilu.com/crm/，该如何配置直接使用域名http://crm.mingguilu.com访问呢？

![](/images/20180520/20180520_01.png)

<!-- more -->

#### 先模拟一下CRM系统扩容

因为当前测试环境无法完全使用war包模拟出使用二级目录访问CRM系统，以下将虚拟主机的根目录设置为/usr/share/nginx/system，CRM系统文件放置在/usr/share/nginx/system/crm下

##### 创建虚拟目录，上传CRM系统文件

    [root@mg-nginx-04 conf.d]# cd /usr/share/nginx/
    [root@mg-nginx-04 nginx]# mkdir -p system/crm
    [root@mg-nginx-04 nginx]# cd system/crm/
    [root@mg-nginx-04 crm]# rz
    [root@mg-nginx-04 crm]# ls
    index.html  sugarcrm.jpg

##### 创建虚拟主机配置文件

    [root@mg-nginx-04 ~]# cd /etc/nginx/conf.d/
    [root@mg-nginx-04 conf.d]# cp -a default.conf system.conf
    [root@mg-nginx-04 conf.d]# vi system.conf
    [root@mg-nginx-04 conf.d]# cat system.conf
    server {
        listen       8088;
        server_name  localhost;

        location / {
            root   /usr/share/nginx/system;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }

##### 启动nginx，访问模拟的CRM系统

    [root@mg-nginx-04 crm]# nginx -t
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful
    [root@mg-nginx-04 crm]#
    [root@mg-nginx-04 crm]# nginx -s reload
    [root@mg-nginx-04 crm]# systemctl restart nginx

访问http://172.29.23.49:8088/crm/index.html，测试成功

![](/images/20180520/20180520_02.png)

访问http://172.29.23.48:8088/crm/index.html，测试成功

![](/images/20180520/20180520_03.png)

####  配置keepalived+nginx高可用

#####  nginx负载均衡配置文件

下面以主负载均衡服务器为例，备负载均衡服务器上的配置相同

    [root@mg-nginx-01 ~]# cd /etc/nginx/conf.d/
    [root@mg-nginx-01 conf.d]#
    [root@mg-nginx-01 conf.d]# vi system.conf
    [root@mg-nginx-01 conf.d]#
    [root@mg-nginx-01 conf.d]# cat system.conf
    upstream system {
            server 172.29.23.48:8088 weight=4 max_fails=2 fail_timeout=30s;
            server 172.29.23.49:8088 weight=4 max_fails=2 fail_timeout=30s;
    }

    server {
        listen       80;
            server_name  crm.mingguilu.com;
            access_log  /var/log/nginx/system.log main;

        location / {
            proxy_pass         http://system;
            proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr;
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
            proxy_next_upstream error timeout invalid_header http_404 http_500 http_502 http_504;
        }
    }
    [root@mg-nginx-01 conf.d]# nginx -t
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful
    [root@mg-nginx-01 conf.d]#
    [root@mg-nginx-01 conf.d]# nginx -s reload

##### keepalived配置文件

keepalived虚拟ip为172.29.19.18，详细配置过程省略，参考上一篇博文：[Keepalived+Nginx实现主备负载均衡高可用]{http://blog.mingguilu.com/2018/05/19/Keepalived-Nginx%E5%AE%9E%E7%8E%B0%E4%B8%BB%E5%A4%87%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E9%AB%98%E5%8F%AF%E7%94%A8/}

##### 客户端添加本地hosts解析

在生产环境中应通知域名或DNS系统管理员，绑定域名crm.mingguilu.com到keepalived虚拟ip 172.29.19.18

    172.29.19.18  crm.mingguilu.com

##### 访问测试

访问 http://crm.mingguilu.com，访问失败

![](/images/20180520/20180520_04.png)

访问 http://crm.mingguilu.com/crm/，访问成功

注意:  http://crm.mingguilu.com/crm/  crm后面必须加上斜杠 / ，否则会找不到二级目录crm下的默认文件index.html，导致访问失败

![](/images/20180520/20180520_05.png)

#### 配置域名访问时自动定向到二级目录

##### proxy_pass的URL加上URI

修改nginx负载均衡配置文件，将proxy_pass    http://system; 修改为 proxy_pass    http://system/crm/;

![](/images/20180520/20180520_06.png)

##### 访问测试

访问 http://crm.mingguilu.com ，访问成功

![](/images/20180520/20180520_07.png)

#### proxy_pass解析

proxy_pass 可以根据location匹配的情况建立被代理的服务器或URI与代理服务器直接的映射关系

##### 语法

* 设置被代理的服务器的协议和地址
* 支持URI映射(可选)
* 协议支持http和https
* 地址支持域名和IP地址，端口号可选

      Syntax: proxy_pass URL;
      Default: —
      Context: location, if in location, limit_except

      Sets the protocol and address of a proxied server and an optional URI to which a location should be mapped. As a protocol, “http” or “https” can be specified. The address can be specified as a domain name or IP address, and an optional port:

      示例：
      proxy_pass http://localhost:8000/uri/; 

* 如果一个域名解析到多个地址，所有地址可能以轮询方式被访问，可以把一个地址指定为一组服务器

      If a domain name resolves to several addresses, all of them will be used in a round-robin fashion. In addition, an address can be specified as a server group.

      示例：
      upstream system {                                                  #定义一组服务器集合，名称为system
              server 172.29.23.48:8088 weight=4 max_fails=2 fail_timeout=30s;
              server 172.29.23.49:8088 weight=4 max_fails=2 fail_timeout=30s;
      }

      server {
          listen       80;
          server_name  crm.mingguilu.com;
          access_log  /var/log/nginx/system.log main;
          location / {
              proxy_pass         http://system;                        #将被代理的服务器地址指定到名称为system的一组服务器
          }
      }

* 参数值支持变量，这种情况下， 如果地址被指定为域名，则在定义的服务器组中搜索名称，如果找不到，将会使用DNS解析

      Parameter value can contain variables. In this case, if an address is specified as a domain name, the name is searched among the described server groups, and, if not found, is determined using a resolver.

##### 请求的URI按照如下规则传递到服务器

* 如果proxy_pass的URL定向里包括URI，那么请求中匹配到location中URI的部分会被proxy_pass后面URL中的URI替换

      A request URI is passed to the server as follows:
      * If the proxy_pass directive is specified with a URI, then when a request is passed to the server, the part of a normalized request URI matching the location is replaced by a URI specified in the directive:
      location /name/ {
          proxy_pass http://127.0.0.1/remote/;
      }
      #请求http://127.0.0.1/name/index.html ，会被代理到 http://127.0.0.1/remote/index.html
      #注意请求 http://127.0.0.1/name 时 /name 必须手动输入完整

* 如果proxy_pass的URL定向里不包括URI，那么请求中的URI会保持原样传送给后端server

      * If proxy_pass is specified without a URI, the request URI is passed to the server in the same form as sent by a client when the original request is processed, or the full normalized request URI is passed when processing the changed URI:
      location /some/path/ {
          proxy_pass http://127.0.0.1;
      }
      #请求http://127.0.0.1/some/path/index.html ，会被代理到 http://127.0.0.1/some/path/index.html
      #注意请求 http://127.0.0.1/some/path  时 /some/path 必须手动输入完整


##### 一些情况下，不能确定替换的URI

* location里是正则表达式，这种情况下，proxy_pass里最好不要有URI

      When location is specified using a regular expression, and also inside named locations.
      In these cases, proxy_pass should be specified without a URI.

* 在proxy_pass前面用了rewrite，如下，这种情况下，proxy_pass是无效的

      When the URI is changed inside a proxied location using the rewrite directive, and this same configuration will be used to process a request (break):
      location /name/ {
        rewrite /name/([^/]+) /users?name=$1 break;
        proxy_pass http://127.0.0.1;
      }

* 如果proxy_pass中包含变量， 它将按原样传递给服务器，替换原始请求URI

      location /name/ {
          proxy_pass http://127.0.0.1$request_uri;
      }

#### 参考文档

* 《Keepalived+Nginx实现主备负载均衡高可用》：[http://blog.mingguilu.com/2018/05/19/Keepalived-Nginx%E5%AE%9E%E7%8E%B0%E4%B8%BB%E5%A4%87%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E9%AB%98%E5%8F%AF%E7%94%A8/](http://blog.mingguilu.com/2018/05/19/Keepalived-Nginx%E5%AE%9E%E7%8E%B0%E4%B8%BB%E5%A4%87%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E9%AB%98%E5%8F%AF%E7%94%A8/)

* 《Module ngx_http_proxy_module proxy_pass》：[http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass) 

* 《Nginx 之四： Nginx服务器的rewrite、全局变量、重定向和防盗链相关功能》：[https://www.cnblogs.com/zhang-shijie/p/5453249.html](https://www.cnblogs.com/zhang-shijie/p/5453249.html)

