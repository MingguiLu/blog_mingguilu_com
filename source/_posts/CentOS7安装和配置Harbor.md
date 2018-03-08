---
title: CentOS7安装和配置Harbor
description: ''
photos: /images/photos/harbor.png
date: 2018-03-08 00:22:48
tags:
- Linux
- CentOS
- Docker
categories: 技术分享
---

#### Harbor安装方式

* 在线安装： 安装包从Docker Hub下载Harbor镜像，所以安装程序很小，但安装时间较长

* 离线安装： 当无法联网时使用，安装包较大，安装过程更快速和稳定，推荐使用

* OVA安装： 在VMware vCente平台下使用

#### 安装要求

* Python:  2.7 或更高版本

* Docker： 1.10 或更高版本

* Docker Compose： 1.6.0 或更高版本

* Openssl： 最新版

#### 安装步骤

* 下载安装包

* 配置harbor.cfg

* 运行install.sh安装并启动

<!--more-->

#### 下载安装包

Harbor最新安装包下载地址： [Harbor Releases Download Binary](https://github.com/vmware/harbor/releases)

##### 下载并解压在线安装包

    [mingguilu@localhost ~]$ wget https://storage.googleapis.com/harbor-releases/release-1.4.0/harbor-online-installer-v1.4.0.tgz

    [mingguilu@localhost ~]$ tar xvf harbor-online-installer-v1.4.0.tgz

##### 下载并解压离线安装包

    [mingguilu@localhost ~]$ wget https://storage.googleapis.com/harbor-releases/release-1.4.0/harbor-offline-installer-v1.4.0.tgz

    [mingguilu@localhost ~]$ tar xvf harbor-offline-installer-v1.4.0.tgz

#### 修改配置文件harbor.cfg

这段内容转自：[Harbor 私有仓库简单部署](http://blog.csdn.net/CSDN_duomaomao/article/details/78036331),是官方文档很完整的汉化文档

##### 必须参数

* hostname：目标主机的主机名，用于访问UI和注册表服务。它应该是目标机器的IP地址或完全限定域名（FQDN），例如192.168.1.10或reg.yourdomain.com。不要使用localhost或127.0.0.1为主机名 – 注册表服务需要外部客户端访问！

* ui_url_protocol：（http或https。默认为http）用于访问UI和令牌/通知服务的协议。如果启用公证，则此参数必须为https。默认情况下，这是http。要设置https协议，请参阅使用HTTPS访问harbor。
    
* db_password：用于db_auth的MySQL数据库的根密码。更改此密码以供任何生产用途！
    
* max_job_workers：（默认值为3）作业服务中的最大复制工作数。对于每个映像复制作业，工作程序将存储库的所有标签同步到远程目标。增加此数字允许系统中更多的并发复制作业。但是，由于每个工作人员都会消耗一定数量的网络/ CPU / IO资源，请根据主机硬件资源选择该属性的值。
    
* customize_crt：（打开或关闭，默认为打开）当此属性打开时，准备脚本将为注册表令牌的生成/验证创建私钥和根证书。当密钥和根证书由外部源提供时，将此属性设置为off。有关详细信息，请参阅自定义密钥和harbor令牌服务证书。
    
* ssl_cert：SSL证书的路径，仅当协议设置为https时才应用
    
* ssl_cert_key：SSL密钥的路径，仅当协议设置为https时才应用
    
* secretkey_path：用于在复制策略中加密或解密远程注册表的密码的密钥路径。

##### 可选参数

* 电子邮件设置：Harbor需要这些参数才能向用户发送“密码重设”电子邮件，只有在需要该功能时才需要这些参数。另外，请注意，在默认情况下SSL连接时没有启用-如果你的SMTP服务器需要SSL，但不支持STARTTLS，那么你应该通过设置启用SSL email_ssl = TRUE。
        email_server = smtp.mydomain.com
        email_server_port = 25
        email_username = sample_admin@mydomain.com
        email_password = abc
        email_from = admin sample_admin@mydomain.com
        email_ssl = false
* harbor_admin_password：管理员的初始密码。该密码仅在Harbor 第一次启动时生效。之后，此设置将被忽略，并且应在UI中设置管理员的密码。请注意，默认用户名/密码为admin / Harbor12345。
 
* auth_mode：使用的身份验证类型。默认情况下，它是db_auth，即凭据存储在数据库中。对于LDAP身份验证，请将其设置为ldap_auth。重要提示：从现有的Harbor 实例升级时，必须确保auth_modeharbor.cfg在启动新版本的Harbor之前是一样的。否则，升级后用户可能无法登录。

* ldap_url：LDAP端点URL（例如ldaps://ldap.mydomain.com）。 仅当auth_mode设置为ldap_auth时才使用。

* ldap_searchdn：具有搜索LDAP / AD服务器权限的用户的DN（例如uid=admin,ou=people,dc=mydomain,dc=com）。

* ldap_search_pwd：由ldap_searchdn指定的用户的密码。

* LDAP_BASEDN：基本DN查找用户，如ou=people,dc=mydomain,dc=com。 仅当auth_mode设置为ldap_auth时才使用。

* LDAP_FILTER：用于查找用户，例如，搜索过滤器(objectClass=person)。

* ldap_uid：用于在LDAP搜索期间匹配用户的属性，它可以是uid，cn，电子邮件或其他属性。

* ldap_scope：搜索用户的范围，1-LDAP_SCOPE_BASE，2-LDAP_SCOPE_ONELEVEL，3-LDAP_SCOPE_SUBTREE。默认值为3。

* self_registration：（开或关，默认为开）启用/禁用用户注册自己的能力。禁用时，只能由管理员用户创建新用户，只有管理员用户才能在海港创建新用户。 注意：当auth_mode设置为ldap_auth时，自注册功能始终被禁用，并且该标志被忽略。

* token_expiration：令牌服务创建的令牌的到期时间（以分钟为单位），默认值为30分钟。

* project_creation_restriction：用于控制用户有权创建项目的标志。默认情况下，每个人都可以创建一个项目，设置为“adminonly”，以便只有admin才能创建项目。

* verify_remote_cert：（上或关闭，默认为上）该标志，判断是否验证SSL / TLS证书时码头与远程注册表实例通信。将此属性设置为off可绕过SSL / TLS验证，SSL / TLS验证通常在远程实例具有自签名或不受信任的证书时使用

##### 配置存储后端（可选）

默认情况下，Harbor将映像存储在本地文件系统上。在生产环境中，您可以考虑使用其他存储后端而不是本地文件系统，如S3，Openstack Swift，Ceph等。您需要更新的是storage文件中的部分common/templates/registry/config.yml。例如，如果您使用Openstack Swift作为存储后端，则该部分可能如下所示：

    storage:
    swift:
        username: admin
        password: ADMIN_PASS
        authurl: http://keystone_addr:35357/v3/auth
        tenant: admin
        domain: default
        region: regionOne
        container: docker_images

注意：有关注册表的存储后端的详细信息，请参阅registry配置参考。

#### 安装Harbor(默认安装，无公证)

    [mingguilu@localhost harbor]$ sudo ./install.sh 

    [Step 0]: checking installation environment ...

    Note: docker version: 17.06.2
    ✖ Need to install docker-compose(1.7.1+) by yourself first and run this script again.

如果之前是使用curl方式安装的Docker-compose，如无意外，安装程序会提示docker-compose未安装，需要使用pip方式重新安装，参考上一篇文章：[CentOS7安装Docker和Docker-compose](http://blog.mingguilu.com/2018/03/06/CentOS7%E5%AE%89%E8%A3%85Docker%E5%92%8CDocker-compose/)

    [mingguilu@localhost harbor]$ sudo ./install.sh 

    [Step 0]: checking installation environment ...

    Note: docker version: 17.06.2

    Note: docker-compose version: 1.19.0

    [Step 1]: loading Harbor images ...
    651f69aef02c: Loading layer [==================================================>]  135.8MB/135.8MB
    40a1aad64343: Loading layer [==================================================>]  23.24MB/23.24MB
    3fe2713e4072: Loading layer [==================================================>]  12.16MB/12.16MB
    ba3a1eb0e375: Loading layer [==================================================>]   17.3MB/17.3MB
    447427ec5e1a: Loading layer [==================================================>]  15.87kB/15.87kB
    4ccb4026663c: Loading layer [==================================================>]  3.072kB/3.072kB
    16faa95946a1: Loading layer [==================================================>]  29.46MB/29.46MB
    Loaded image: vmware/notary-server-photon:v0.5.1-v1.4.0
    fa7ba9fd42c9: Loading layer [==================================================>]  10.95MB/10.95MB
    4e400f9ae23e: Loading layer [==================================================>]   17.3MB/17.3MB
    2802fb27c88b: Loading layer [==================================================>]  15.87kB/15.87kB
    e6367a4e1e1e: Loading layer [==================================================>]  3.072kB/3.072kB
    8ece8dfcdd98: Loading layer [==================================================>]  28.24MB/28.24MB
    Loaded image: vmware/notary-signer-photon:v0.5.1-v1.4.0
    a7dd1a8afcaf: Loading layer [==================================================>]  396.7MB/396.7MB
    05adebbe496f: Loading layer [==================================================>]  9.216kB/9.216kB
    86eb534949fa: Loading layer [==================================================>]  9.216kB/9.216kB
    d7f127c69380: Loading layer [==================================================>]   7.68kB/7.68kB
    5ac1c4dc5ee9: Loading layer [==================================================>]  1.536kB/1.536kB
    d0bec56b5b1a: Loading layer [==================================================>]  9.728kB/9.728kB
    4bbe83860556: Loading layer [==================================================>]   2.56kB/2.56kB
    e526f9e6769f: Loading layer [==================================================>]  3.072kB/3.072kB
    Loaded image: vmware/harbor-db:v1.4.0
    1cff102bbda2: Loading layer [==================================================>]  154.1MB/154.1MB
    04c9f3e07de1: Loading layer [==================================================>]  10.75MB/10.75MB
    7b6c7bf54f5c: Loading layer [==================================================>]  2.048kB/2.048kB
    42f8acdb7fe3: Loading layer [==================================================>]  48.13kB/48.13kB
    5b6299d0a1df: Loading layer [==================================================>]   10.8MB/10.8MB
    Loaded image: vmware/clair-photon:v2.0.1-v1.4.0
    6534131f457c: Loading layer [==================================================>]  94.76MB/94.76MB
    73f582101e4b: Loading layer [==================================================>]  6.656kB/6.656kB
    86d847823c48: Loading layer [==================================================>]  6.656kB/6.656kB
    Loaded image: vmware/postgresql-photon:v1.4.0
    5cd250d5a352: Loading layer [==================================================>]  23.24MB/23.24MB
    ad3fd52b54f3: Loading layer [==================================================>]  14.99MB/14.99MB
    13b1e24cc368: Loading layer [==================================================>]  14.99MB/14.99MB
    Loaded image: vmware/harbor-adminserver:v1.4.0
    c26c69706710: Loading layer [==================================================>]  23.24MB/23.24MB
    223f6fe02cc8: Loading layer [==================================================>]  23.45MB/23.45MB
    1fc843c8698a: Loading layer [==================================================>]  7.168kB/7.168kB
    e09293610ee7: Loading layer [==================================================>]  10.39MB/10.39MB
    d59f9780b1d8: Loading layer [==================================================>]  23.44MB/23.44MB
    Loaded image: vmware/harbor-ui:v1.4.0
    dd4753242e59: Loading layer [==================================================>]  73.07MB/73.07MB
    95aed61ca251: Loading layer [==================================================>]  3.584kB/3.584kB
    1864f9818562: Loading layer [==================================================>]  3.072kB/3.072kB
    da2a19f80b81: Loading layer [==================================================>]  4.096kB/4.096kB
    058531639e75: Loading layer [==================================================>]  3.584kB/3.584kB
    a84e69fb619b: Loading layer [==================================================>]  10.24kB/10.24kB
    Loaded image: vmware/harbor-log:v1.4.0
    b1056051f246: Loading layer [==================================================>]  23.24MB/23.24MB
    07678065e08b: Loading layer [==================================================>]  19.19MB/19.19MB
    a2d9bdb8f5fb: Loading layer [==================================================>]  19.19MB/19.19MB
    Loaded image: vmware/harbor-jobservice:v1.4.0
    7f58ce57cd5e: Loading layer [==================================================>]  4.805MB/4.805MB
    Loaded image: vmware/nginx-photon:v1.4.0
    4c8965978b77: Loading layer [==================================================>]  23.24MB/23.24MB
    1466c942edde: Loading layer [==================================================>]  2.048kB/2.048kB
    ac5c17331735: Loading layer [==================================================>]  2.048kB/2.048kB
    86824c7c466a: Loading layer [==================================================>]  2.048kB/2.048kB
    fd3bd0e70d67: Loading layer [==================================================>]   22.8MB/22.8MB
    b02195d77636: Loading layer [==================================================>]   22.8MB/22.8MB
    Loaded image: vmware/registry-photon:v2.6.2-v1.4.0
    Loaded image: vmware/photon:1.0
    Loaded image: vmware/mariadb-photon:v1.4.0
    454c81edbd3b: Loading layer [==================================================>]  135.2MB/135.2MB
    e99db1275091: Loading layer [==================================================>]  395.4MB/395.4MB
    051e4ee23882: Loading layer [==================================================>]  9.216kB/9.216kB
    6cca4437b6f6: Loading layer [==================================================>]  9.216kB/9.216kB
    1d48fc08c8bc: Loading layer [==================================================>]   7.68kB/7.68kB
    0419724fd942: Loading layer [==================================================>]  1.536kB/1.536kB
    526b2156bd7a: Loading layer [==================================================>]  637.8MB/637.8MB
    9ebf6900ecbd: Loading layer [==================================================>]  78.34kB/78.34kB
    Loaded image: vmware/harbor-db-migrator:1.4


    [Step 2]: preparing environment ...
    loaded secret from file: /data/secretkey
    Generated configuration file: ./common/config/nginx/nginx.conf
    Generated configuration file: ./common/config/adminserver/env
    Generated configuration file: ./common/config/ui/env
    Generated configuration file: ./common/config/registry/config.yml
    Generated configuration file: ./common/config/db/env
    Generated configuration file: ./common/config/jobservice/env
    Generated configuration file: ./common/config/log/logrotate.conf
    Generated configuration file: ./common/config/jobservice/app.conf
    Generated configuration file: ./common/config/ui/app.conf
    Generated certificate, key file: ./common/config/ui/private_key.pem, cert file: ./common/config/registry/root.crt
    The configuration files are ready, please use docker-compose to start the service.
    Creating harbor-log ... done

    [Step 3]: checking existing instance of Harbor ...
    Creating harbor-adminserver ... done
    Creating harbor-ui ... done
    [Step 4]: starting Harbor ...
    Creating nginx ... done
    Creating harbor-db ... 
    Creating registry ... 
    Creating harbor-adminserver ... 
    Creating harbor-ui ... 
    Creating harbor-jobservice ... 
    Creating nginx ... 

    ✔ ----Harbor has been installed and started successfully.----

    Now you should be able to visit the admin portal at http://192.168.2.200. 
    For more details, please visit https://github.com/vmware/harbor .

#### 访问Harbor门户

在浏览器访问*http://hostname*,Harbor默认管理员账户是admin，密码是Harbor12345

![](/images/20180308/20180308_07_01.png)

![](/images/20180308/20180308_07_02.png)

#### 管理Harbor

##### 停止harbor

    [mingguilu@localhost harbor]$ sudo docker-compose stop
    Stopping harbor-jobservice  ... done
    Stopping nginx              ... done
    Stopping harbor-ui          ... done
    Stopping harbor-db          ... done
    Stopping harbor-adminserver ... done
    Stopping registry           ... done
    Stopping harbor-log         ... done

##### 启动harbor

    [mingguilu@localhost harbor]$ sudo docker-compose start
    Starting log         ... done
    Starting adminserver ... done
    Starting registry    ... done
    Starting ui          ... done
    Starting mysql       ... done
    Starting jobservice  ... done
    Starting proxy       ... done

##### 查看harbor状态

    [mingguilu@localhost harbor]$ sudo docker-compose ps
        Name                     Command               State                                Ports                              
    ------------------------------------------------------------------------------------------------------------------------------
    harbor-adminserver   /harbor/start.sh                 Up                                                                      
    harbor-db            /usr/local/bin/docker-entr ...   Up      3306/tcp                                                        
    harbor-jobservice    /harbor/start.sh                 Up                                                                      
    harbor-log           /bin/sh -c /usr/local/bin/ ...   Up      127.0.0.1:1514->10514/tcp                                       
    harbor-ui            /harbor/start.sh                 Up                                                                      
    nginx                nginx -g daemon off;             Up      0.0.0.0:443->443/tcp, 0.0.0.0:4443->4443/tcp, 0.0.0.0:80->80/tcp
    registry             /entrypoint.sh serve /etc/ ...   Up      5000/tcp                                                        

##### 更改harbor的配置

    $ sudo docker-compose down -v
    $ vim harbor.cfg
    $ sudo ./prepare
    $ sudo docker-compose up -d

##### 删除Harbor 的容器，同时保留图像数据和Harbor的数据库文件在文件系统上

    $ sudo docker-compose down -v

##### 删除Harbor 的数据库和图像数据（为了干净的重新安装）

    $ rm -rf /data/database
    $ rm -rf /data/registry

#### 客户端登录

##### 登录失败

    [mingguilu@localhost harbor]$ sudo docker login -u admin -p Harbor12345 192.168.2.200
    Error response from daemon: Get https://192.168.2.200/v2/: dial tcp 192.168.2.200:443: getsockopt: connection refused

使用docker login命令登录时报错了，这是因为默认情况下，docker对registry的操作是基于https协议的，而Harbor默认是以http协议访问的

其实在harbor官方安装配置文档中，就有下面一段做了说明：

    IMPORTANT: The default installation of Harbor uses HTTP - as such, you will need to add the option --insecure-registry to your client's Docker daemon and restart the Docker service.

    重要信息： Harbor的默认安装使用HTTP – 因此，您需要将该选项添加--insecure-registry到客户端的Docker守护程序中，然后重新启动Docker服务。

##### 添加insecure-registry选项

我们去Docker守护进程中(/etc/docker/daemon.json ,CentOS7下没有这个文件，需要手动创建)添加 --insecure-registry 参数

    [mingguilu@localhost harbor]$ sudo vim /etc/docker/daemon.json
    [mingguilu@localhost harbor]$ sudo cat /etc/docker/daemon.json
    { "insecure-registries":["192.168.2.200"] }

##### 重启docker

    [mingguilu@localhost harbor]$ sudo systemctl restart docker

##### 重启harbor
    [mingguilu@localhost harbor]$ sudo docker-compose stop
    Stopping harbor-jobservice  ... done
    Stopping nginx              ... done
    Stopping harbor-ui          ... done
    Stopping harbor-db          ... done
    Stopping harbor-adminserver ... done
    Stopping registry           ... done
    Stopping harbor-log         ... done

    [mingguilu@localhost harbor]$ sudo docker-compose start
    Starting log         ... done
    Starting adminserver ... done
    Starting registry    ... done
    Starting ui          ... done
    Starting mysql       ... done
    Starting jobservice  ... done
    Starting proxy       ... done

##### 登录成功

    [mingguilu@localhost harbor]$ sudo docker login -u admin -p Harbor12345 192.168.2.200
    Login Succeeded

#### 上传镜像

##### 给镜像打tag

    docker tag repository:tag hostname:port/projectname/repository:tag

以hello-world镜像为例

    [mingguilu@localhost harbor]$ sudo docker tag hello-world 192.168.2.200/library/hello-world:test
    
    [mingguilu@localhost harbor]$ sudo docker images | grep hello-world
    192.168.2.200/library/hello-world   test                f2a91732366c        3 months ago        1.85kB
    hello-world                         latest              f2a91732366c        3 months ago        1.85kB

##### 上传镜像

    [mingguilu@localhost harbor]$ sudo docker push 192.168.2.200/library/hello-world:test
    The push refers to a repository [192.168.2.200/library/hello-world]
    f999ae22f308: Layer already exists 
    test: digest: sha256:8072a54ebb3bc136150e2f2860f00a7bf45f13eeb917cca2430fcd0054c8e51b size: 524

    [mingguilu@localhost harbor]$ sudo docker push 192.168.2.200/library/harbor-log:test
    The push refers to a repository [192.168.2.200/library/harbor-log]
    a84e69fb619b: Layer already exists 
    058531639e75: Layer already exists 
    da2a19f80b81: Layer already exists 
    1864f9818562: Layer already exists 
    95aed61ca251: Layer already exists 
    dd4753242e59: Layer already exists 
    651f69aef02c: Layer already exists 
    test: digest: sha256:6b7e914eadc358c17929b0e7230ea951e5962441485e85c7245d2b25b601743a size: 1777

##### 在Harbor门户在管理镜像

![](/images/20180308/20180308_10_01.png)

##### 拉取镜像

    [mingguilu@localhost harbor]$ sudo docker rmi 192.168.2.200/library/harbor-log:test
    Untagged: 192.168.2.200/library/harbor-log:test
    Untagged: 192.168.2.200/library/harbor-log@sha256:6b7e914eadc358c17929b0e7230ea951e5962441485e85c7245d2b25b601743a

    [mingguilu@localhost harbor]$ sudo docker pull 192.168.2.200/library/harbor-log:test 
    test: Pulling from library/harbor-log
    Digest: sha256:6b7e914eadc358c17929b0e7230ea951e5962441485e85c7245d2b25b601743a
    Status: Downloaded newer image for 192.168.2.200/library/harbor-log:test

    [mingguilu@localhost harbor]$ sudo docker images | grep harbor-log
    192.168.2.200/library/harbor-log    test                9e818c7a27ab        4 weeks ago         200MB
    vmware/harbor-log                   v1.4.0              9e818c7a27ab        4 weeks ago         200MB

---

### 参考文档

* 《Installation and Configuration Guide》: [https://github.com/vmware/harbor/blob/master/docs/installation_guide.md](https://github.com/vmware/harbor/blob/master/docs/installation_guide.md)

* 《Harbor 私有仓库简单部署》： [http://blog.csdn.net/CSDN_duomaomao/article/details/78036331](http://blog.csdn.net/CSDN_duomaomao/article/details/78036331)

* 《Docker仓库Harbor》： [https://www.itwithauto.com/?p=785](https://www.itwithauto.com/?p=785)

* 《Harbor实战》： [https://www.jianshu.com/p/cf16763942d5](https://www.jianshu.com/p/cf16763942d5)


