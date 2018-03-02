---
title: Docker入门 
description: ''
photos: /images/photos/docker.jpg
date: 2018-03-01 11:59:02
tags:
- Docker
- container
- 容器
categories: 技术分享
---

#### Docker简介

Docker是一个开源的引擎，可以轻松的为任何应用创建一个轻量级的、可移植的、自给自足的容器。开发者在笔记本上编译测试通过的容器可以批量地在生产环境中部署，包括VMs（虚拟机）、bare metal、OpenStack 集群和其他的基础应用平台器

<!--more-->

#### 安装Docker

我当前使用的系统版本是： Linux Mint 18.2 Cinnamon 64-bit

可以参考官方文档使用`apt-get`安装：[Get Docker CE for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/) ,也可通过下面命令安装

##### 使用命令安装最新版Docker： 

        mingguilu@Brick ~ $ sudo wget -qO- https://get.docker.com | sh
        [sudo] mingguilu 的密码： 
        # Executing docker install script, commit: 5f7af95
        + sudo -E sh -c apt-get update -qq >/dev/null
        + sudo -E sh -c apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
        + sudo -E sh -c curl -fsSL "https://download.docker.com/linux/debian/gpg" | apt-key add -qq - >/dev/null
        + sudo -E sh -c echo "deb [arch=amd64] https://download.docker.com/linux/debian stretch edge" > /etc/apt/sources.list.d/docker.list
        + [ debian = debian ]
        + [ stretch = wheezy ]
        + sudo -E sh -c apt-get update -qq >/dev/null
        + sudo -E sh -c apt-get install -y -qq --no-install-recommends docker-ce >/dev/null
        + sudo -E sh -c docker version
        Client:
        Version:	18.02.0-ce
        API version:	1.36
        Go version:	go1.9.3
        Git commit:	fc4de44
        Built:	Wed Feb  7 21:16:25 2018
        OS/Arch:	linux/amd64
        Experimental:	false
        Orchestrator:	swarm

        Server:
        Engine:
        Version:	18.02.0-ce
        API version:	1.36 (minimum version 1.12)
        Go version:	go1.9.3
        Git commit:	fc4de44
        Built:	Wed Feb  7 21:14:59 2018
        OS/Arch:	linux/amd64
        Experimental:	false
        If you would like to use Docker as a non-root user, you should now consider
        adding your user to the "docker" group with something like:

        sudo usermod -aG docker mingguilu

        Remember that you will have to log out and back in for this to take effect!

        WARNING: Adding a user to the "docker" group will grant the ability to run
                containers which can be used to obtain root privileges on the
                docker host.
                Refer to https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface
                for more information.

注意安装完后提示需要将非root权限用户添加到docker组，这样运行docker命令前不需要sudo了

##### 添加用户到`docker`组 

     sudo usermod -aG docker mingguilu

##### 查看已安装的Docker信息

        mingguilu@Brick ~ $ docker info
        Containers: 0
        Running: 0
        Paused: 0
        Stopped: 0
        Images: 0
        Server Version: 18.02.0-ce
        Storage Driver: overlay2
        Backing Filesystem: extfs
        Supports d_type: true
        Native Overlay Diff: true
        Logging Driver: json-file
        Cgroup Driver: cgroupfs
        Plugins:
        Volume: local
        Network: bridge host macvlan null overlay
        Log: awslogs fluentd gcplogs gelf journald json-file logentries splunk syslog
        Swarm: inactive
        Runtimes: runc
        Default Runtime: runc
        Init Binary: docker-init
        containerd version: 9b55aab90508bd389d7654c4baf173a981477d55
        runc version: 9f9c96235cc97674e935002fc3d78361b696a69e
        init version: 949e6fa
        Security Options:
        apparmor
        seccomp
        Profile: default
        Kernel Version: 4.8.0-53-generic
        Operating System: Linux Mint 18.2
        OSType: linux
        Architecture: x86_64
        CPUs: 4
        Total Memory: 11.46GiB
        Name: Brick
        ID: 2NP7:D6CP:UHT4:PJP2:IXBT:LTP6:PL3N:EXVB:TA4O:C2WR:IP6N:AYQM
        Docker Root Dir: /var/lib/docker
        Debug Mode (client): false
        Debug Mode (server): false
        Registry: https://index.docker.io/v1/
        Labels:
        Experimental: false
        Insecure Registries:
        127.0.0.0/8
        Live Restore Enabled: false

        WARNING: No swap limit support

#### Docker的基本架构和常用命令

##### Docker的基本架构

![](/images/20180301/20180301_03_01.png)

![](/images/20180301/20180301_03_02.png)

##### Docer的常用命令

* docker pull           拉取image(镜像)
* docker build          创建image 
* docker images         列出image
* docker run            运行container(容器)
* docker ps             列出正在运行的container
* docker ps -a          列出包括停止的container
* docker rm             删除已经停止的container
* docker rmi            删除image
* docker cp             在host和container之间拷贝文件
* docker commit         保存改动为新的image

更多的docker命令可执行docker或docker --help查看

#### Docker命令实战

##### Docker安装完成时host上还没有任何的镜像和容器

        mingguilu@Brick ~/helloworld/docker $ docker images
        REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
        mingguilu@Brick ~/helloworld/docker $ 
        mingguilu@Brick ~/helloworld/docker $ docker ps -a
        CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
        mingguilu@Brick ~/helloworld/docker $

##### 使用ubuntu的镜像运行一个容器，用来打印一句"hello docker"

        mingguilu@Brick ~/helloworld/docker $ docker run ubuntu echo hello docker
        Unable to find image 'ubuntu:latest' locally
        latest: Pulling from library/ubuntu
        1be7f2b886e8: Pull complete 
        6fbc4a21b806: Pull complete 
        c71a6f8e1378: Pull complete 
        4be3072e5a37: Pull complete 
        06c6d2f59700: Pull complete 
        Digest: sha256:e46251ecd3548065ca203df31bdcf25e53b530b9b39bd3a86ac8147b347bb7ac
        Status: Downloaded newer image for ubuntu:latest
        hello docker

由于本地host上并没有ubuntu的镜像，Docker会自动在线上镜像仓库拉取一个最新版的到本地host，并运行一个打印"hello docker"的容器，在最后一行可以看到"hello world"被打印出来

##### 再查看一下镜像和容器

        mingguilu@Brick ~/helloworld/docker $ docker images
        REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
        ubuntu              latest              0458a4468cbc        4 weeks ago         112MB
        mingguilu@Brick ~/helloworld/docker $ 
        mingguilu@Brick ~/helloworld/docker $ docker ps
        mingguilu@Brick ~/helloworld/docker $
        mingguilu@Brick ~/helloworld/docker $ docker ps -a
        CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS                     PORTS               NAMES
        8528c567160        ubuntu              "echo hello docker"   8 minutes ago       Exited (0) 8 minutes ago                       nostalgic_hawking
        mingguilu@Brick ~/helloworld/docker $ 

可以看一个刚拉取的镜像：名称为ubuntu，标签为latest，镜像ID号为0458a4468cbc，创建于4周前，大小为112M

执行`docker ps`没有返回信息，这是因为`Docker ps`只能查看运行中的容器，上面运行的容器在打印出"hello docker"之后就自动停止了

执行`docker ps -a`就可以看到当前停止状态的容器：容器ID号为0458a4468cbc,运行在镜像ubuntu上，执行一条echo hello docker的命令，创建并停止于8分钟之前，没有端口映射信息，（NAMES=nostalgic_hawking 我暂时还不明白）

##### 删除镜像ubuntu

        mingguilu@Brick ~/helloworld/docker $ docker rmi 0458a4468cbc
        Error response from daemon: conflict: unable to delete 0458a4468cbc (must be forced) - image is being used by stopped container 8528c5671605

删除时报错了：无法删除镜像0458a4468cbc,因为停止中的容器0458a4468cbc正在使用；此时必须先删除容器，才能删除镜像

        mingguilu@Brick ~/helloworld/docker $ docker rm 8528c5671605
        8528c5671605
        mingguilu@Brick ~/helloworld/docker $ docker ps -a
        CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
        mingguilu@Brick ~/helloworld/docker $ docker rmi 0458a4468cbc
        Untagged: ubuntu:latest
        Untagged: ubuntu@sha256:e46251ecd3548065ca203df31bdcf25e53b530b9b39bd3a86ac8147b347bb7ac
        Deleted: sha256:0458a4468cbceea0c304de953305b059803f67693bad463dcbe7cce2c91ba670
        Deleted: sha256:77e6ddba346d8ad1e436256f6373dede5af4002006981b7d4116c561c759cefa
        Deleted: sha256:8db758ab2fdb54da0aec53aeac876934337e6170f5a8c8872b3d4171e3d465b7
        Deleted: sha256:a7fc6b405fe8ef71edfa6163d1dc9f1cb1df426049eefaa7d388e9df21a061ad
        Deleted: sha256:5a3e35538f7f2e2727c8ac92f08c30002b9e8a77737de0dab91244344d59f69b
        Deleted: sha256:ff986b10a018b48074e6d3a68b39aad8ccc002cdad912d4148c0f92b3729323e
        mingguilu@Brick ~/helloworld/docker $ docker images
        REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
        mingguilu@Brick ~/helloworld/docker $

#### 使用Dockerfile创建docker镜像

##### 创建一个目录，目录名随意

        mingguilu@Brick ~/helloworld/docker $ mkdir image01
        mingguilu@Brick ~/helloworld/docker $ cd image01/

##### 创建一个名称为Dockerfile的文件

        mingguilu@Brick ~/helloworld/docker/image01 $ touch Dockerfile
        mingguilu@Brick ~/helloworld/docker/image01 $ vim Dockerfile 
        mingguilu@Brick ~/helloworld/docker/image01 $ 
        mingguilu@Brick ~/helloworld/docker/image01 $ cat Dockerfile 
        # 指定基础镜像
        FROM alpine:latest
        # 指定镜像的创作者
        MAINTAINER MingguiLu
        # 指定要执行的命令
        CMD echo "hello docker"


##### 使用Dockerfile文件生成一个镜像

        docker build -t hello_docker:v1.0 .

* -t 可以指定镜像的Name:Tag，如果不指定Tag，缺省为latest
* . 表示把当前路径下的所有内容都提供给docker创建镜像

        mingguilu@Brick ~/helloworld/docker/image01 $ docker build -t hello_docker:v1.0 .
        Sending build context to Docker daemon  2.048kB
        Step 1/3 : FROM alpine:latest
        latest: Pulling from library/alpine
        ff3a5c916c92: Pull complete 
        Digest: sha256:ed8059bd90dd8cd6b7bfddedc7bba0f7555f766a00daf6a6efc86fa3339c09ef
        Status: Downloaded newer image for alpine:latest
        ---> 3fd9065eaf02
        Step 2/3 : MAINTAINER MingguiLu
        ---> Running in 2325cc668fe9
        Removing intermediate container 2325cc668fe9
        ---> ed63ade16825
        Step 3/3 : CMD echo "hello docker"
        ---> Running in 3560d853a35f
        Removing intermediate container 3560d853a35f
        ---> 5fb36ba2abe8
        Successfully built 5fb36ba2abe8
        Successfully tagged hello_docker:v1.0


##### 运行创建的镜像

如果镜像标签不是缺省的latest，需要同时指定Name:Tag，或直接使用镜像ID号

        mingguilu@Brick ~/helloworld/docker $ docker run hello_docker
        Unable to find image 'hello_docker:latest' locally
        docker: Error response from daemon: pull access denied for hello_docker, repository does not exist or may require 'docker login'.
        See 'docker run --help'.
        mingguilu@Brick ~/helloworld/docker/image01 $ 
        mingguilu@Brick ~/helloworld/docker/image01 $ docker run hello_docker:v1.0 
        hello docker
        mingguilu@Brick ~/helloworld/docker/image01 $ docker run 5fb36ba2abe8
        hello docker
 
#### 使用Docker快速搭建一个Nginx网站服务器

##### 拉取一个nginx镜像

        mingguilu@Brick ~/helloworld/docker $ docker pull nginx
        Using default tag: latest
        latest: Pulling from library/nginx
        8176e34d5d92: Pull complete 
        5b19c1bdd74b: Pull complete 
        4e9f6296fa34: Pull complete 
        Digest: sha256:c8db985772160427261dc443e62cab3e07212f7d52a18093d29f561b767bccb2
        Status: Downloaded newer image for nginx:latest
        mingguilu@Brick ~/helloworld/docker $ 
        mingguilu@Brick ~/helloworld/docker $ docker images
        REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
        hello_docker        v1.0                5fb36ba2abe8        37 minutes ago      4.15MB
        nginx               latest              e548f1a579cf        8 days ago          109MB
        alpine              latest              3fd9065eaf02        7 weeks ago         4.15MB

##### 运行nginx镜像（实际是运行一个基于nginx镜像的容器）

        mingguilu@Brick ~/helloworld/docker $ docker run -p 8080:80 -d nginx
        d0dc27c2df7dc531db13407f43f4a98f860f1862e810353073529d14646a0dfe
        mingguilu@Brick ~/helloworld/docker $ 
        mingguilu@Brick ~/helloworld/docker $ docker ps
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
        d0dc27c2df7d        nginx               "nginx -g 'daemon of…"   8 seconds ago       Up 7 seconds        0.0.0.0:8080->80/tcp   suspicious_williams


打开浏览器，访问http://localhost:8080

![](/images/20180301/20180301_06_01.png)

##### 将host上的网页文件拷贝到容器里

创建一个简单的网页文件

        mingguilu@Brick ~/helloworld/docker $ touch index.htm
        mingguilu@Brick ~/helloworld/docker $ vim index.html 
        mingguilu@Brick ~/helloworld/docker $ 
        mingguilu@Brick ~/helloworld/docker $ cat index.html 
        <html>
        <h1>Docker is fun !</h1>
        </html>

 拷贝网页文件到容器的内部

        mingguilu@Brick ~/helloworld/docker $ docker cp index.html d0dc27c2df7d:/usr/share/nginx/html

在浏览器刷新页面

![](/images/20180301/20180301_06_02.png)

此时如果将当前容器停止运行后再重新运行，之前改动过的网页文件就没有了

#### 将改动了网页文件的容器保存为一个新的镜像

        mingguilu@Brick ~/helloworld/docker $ docker commit -m 'fun' d0dc27c2df7d nginx-docker-fun
        sha256:fdf410b966035c9b05ed8dc1167d1ddd7ecc0c269e610672c40e7e399bb2d01b
        mingguilu@Brick ~/helloworld/docker $ 
        mingguilu@Brick ~/helloworld/docker $ docker images
        REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
        nginx-docker-fun    latest              fdf410b96603        21 seconds ago      109MB
        hello_docker        v1.0                5fb36ba2abe8        About an hour ago   4.15MB
        nginx               latest              e548f1a579cf        8 days ago          109MB
        alpine              latest              3fd9065eaf02        7 weeks ago         4.15MB

##### 停止运行当前容器，运行保存的新镜像

        mingguilu@Brick ~/helloworld/docker $ docker ps -a
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
        d0dc27c2df7d        nginx               "nginx -g 'daemon of…"   28 minutes ago      Up 28 minutes       0.0.0.0:8080->80/tcp   suspicious_williams
        mingguilu@Brick ~/helloworld/docker $ 
        mingguilu@Brick ~/helloworld/docker $ docker stop d0dc27c2df7d
        d0dc27c2df7d
        mingguilu@Brick ~/helloworld/docker $ 
        mingguilu@Brick ~/helloworld/docker $ docker run -p 8080:80 -d nginx-docker-fun
        ac52de709f13d7aaa96d8e67ad7bb60c2bbdd7624acb2ab0d9327547ecff1b02

刷新浏览器，依然可以看到"Docker is fun !"

---

* 《Docker入门》课程学习地址： [https://www.imooc.com/learn/867](https://www.imooc.com/learn/867)

* Docker中文社区：[http://www.docker.org.cn/index.html](http://www.docker.org.cn/index.html)

