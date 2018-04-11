---
title: Docker进阶-命令详解 
description: ''
photos: /images/photos/docker.jpg
date: 2018-03-02 11:59:02
tags:
- Docker
- container
- 容器
categories: 运维之道
---

#### Docker版本信息

##### docker info

    docker info [OPTIONS]

    $ sudo docker info
    Containers: 3
    Running: 1
    Paused: 0
    Stopped: 2
    Images: 8
    Server Version: 18.03.0-ce
    Storage Driver: overlay2
    Backing Filesystem: extfs
    Supports d_type: true
    Native Overlay Diff: false
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
    containerd version: cfd04396dc68220d1cecbe686a6cc3aa5ce3667c
    runc version: 4fc53a81fb7c994640722ac585fa9ca548971871
    init version: 949e6fa
    Security Options:
    seccomp
      Profile: default
    Kernel Version: 4.14.30-1-MANJARO
    Operating System: Manjaro Linux
    OSType: linux
    Architecture: x86_64
    CPUs: 4
    Total Memory: 11.63GiB
    Name: brick
    ID: N2PS:JU6L:LEPJ:3WPU:PGGR:6LFK:H5OZ:OYCA:A2KU:FTZE:JVBO:PZGQ
    Docker Root Dir: /var/lib/docker
    Debug Mode (client): false
    Debug Mode (server): false
    Registry: https://index.docker.io/v1/
    Labels:
    Experimental: false
    Insecure Registries:
    127.0.0.0/8
    Live Restore Enabled: false

##### docker version

    docker version [OPTIONS]

    $ sudo docker version
    Client:
    Version:	18.03.0-ce
    API version:	1.37
    Go version:	go1.10
    Git commit:	0520e24302
    Built:	Fri Mar 23 01:47:41 2018
    OS/Arch:	linux/amd64
    Experimental:	false
    Orchestrator:	swarm

    Server:
    Engine:
      Version:	18.03.0-ce
      API version:	1.37 (minimum version 1.12)
      Go version:	go1.10
      Git commit:	0520e24302
      Built:	Fri Mar 23 01:48:12 2018
      OS/Arch:	linux/amd64
      Experimental:	false

##### docker -v

    $ sudo docker -v 
    Docker version 18.03.0-ce, build 0520e24302

<!--more-->

#### Docker镜像仓库

##### docker login

登录到docker镜像仓库：

    docker login [OPTIONS] [SERVER]

    Options:
      -p, 登录密码
      -u, 登录用户名
    Server：
      如果未指定镜像仓库地址，默认为官方仓库 Docker Hub

    $ sudo docker login -u admin -p Harbor12345 172.29.20.54
    WARNING! Using --password via the CLI is insecure. Use --password-stdin.
    Login Succeeded

##### docker logout

登出docker镜像仓库：

    docker logout [SERVER]

    Server：
      如果未指定镜像仓库地址，默认为官方仓库 Docker Hub

    $ sudo docker logout 172.29.20.54
    Removing login credentials for 172.29.20.54

##### docker search

从镜像仓库中查找镜像：

    docker search [OPTIONS] TERM

    Options:
    -f, --filter filter   显示过滤后的搜索结果
        --format string   ？
        --limit int       指定显示的搜索结果数量
        --no-trunc        显示完整的镜像描述

搜索nginx镜像，显示前5个，并显示完整的描述信息

    $ sudo docker search --limit 5 --no-trunc nginx
    NAME                                     DESCRIPTION                                                                      STARS               OFFICIAL            AUTOMATED
    nginx                                    Official build of Nginx.                                                         8309                [OK]                
    jwilder/nginx-proxy                      Automated Nginx reverse proxy for docker containers                              1309                                    [OK]
    richarvey/nginx-php-fpm                  Container running Nginx + PHP-FPM capable of pulling application code from git   544                                     [OK]
    jrcs/letsencrypt-nginx-proxy-companion   LetsEncrypt container to use with nginx as proxy                                 341                                     [OK]
    nginxdemos/nginx-ingress                 NGINX Ingress Controller for Kubernetes                                          10                                      
搜索官方的并且stars数量不少于1000的apache镜像

    $ sudo docker search --filter "is-official=true" --filter "stars=1000" apache
    NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
    tomcat              Apache Tomcat is an open source implementati…   1790                [OK]                
    httpd               The Apache HTTP Server Project                  1618                [OK]


##### docker pull

从镜像仓库中拉取一个镜像：

    docker pull [OPTIONS] NAME[:TAG|@DIGEST]

    Options:
    -a, --all-tags    拉取所有tagged的镜像
        --disable-content-trust   忽略镜像的校验，默认开启

    $ sudo docker pull jenkins
    Using default tag: latest
    latest: Pulling from library/jenkins
    c73ab1c6897b: Pull complete 
    1ab373b3deae: Pull complete 
    b542772b4177: Pull complete 
    57c8de432dbe: Pull complete 
    da44f64ae999: Pull complete 
    0bbc7b377a91: Pull complete 
    1b6c70b3786f: Pull complete 
    d9bbcf733166: Pull complete 
    b1d3e8de8ec6: Pull complete 
    c1455927bc48: Pull complete 
    1d3c626322f1: Pull complete 
    23612106c74c: Pull complete 
    596d10c47bfa: Pull complete 
    62e8d5201cdb: Pull complete 
    2c3d92fb5e98: Pull complete 
    bc5965f9d105: Pull complete 
    6816953234b3: Pull complete 
    e49ca30dec01: Pull complete 
    0713317dfd5a: Pull complete 
    522ab7b13eb6: Pull complete 
    Digest: sha256:93263adb6ab1ecb240b342a9e62e782c5b46d4d87cd01830021d1dfe89acb518
    Status: Downloaded newer image for jenkins:latest

##### docker push

上传本地镜像到镜像仓库，要先登录到镜像仓库：

    docker push [OPTIONS] NAME[:TAG]

    Options:
        --disable-content-trust   忽略镜像的校验，默认开启

    $ sudo docker pull hello-world
    Using default tag: latest
    latest: Pulling from library/hello-world
    Digest: sha256:97ce6fa4b6cdc0790cda65fe7290b74cfebd9fa0c9b8c38e979330d547d22ce1
    Status: Image is up to date for hello-world:latest


#### 本地镜像管理

##### docker images

列出本地镜像：

    docker images [OPTIONS] [REPOSITORY[:TAG]]

    Options:
    -a, --all             列出所有镜像(包含中间映像层)
        --digests         显示摘要信息
    -f, --filter filter   显示满足条件的的镜像
        --format string   指定返回值的模板文件
        --no-trunc        显示完整的镜像信息
    -q, --quiet           只显示镜像ID号

    $ sudo docker images 
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    hello_docker        latest              d68de571b8bb        3 days ago          4.15MB
    nginx               latest              7f70b30f2cc6        2 weeks ago         109MB
    jenkins             latest              7b210b6c238a        2 weeks ago         801MB
    ubuntu              latest              f975c5035748        4 weeks ago         112MB
    centos              latest              2d194b392dd1        4 weeks ago         195MB
    vbkunin/itop        latest              a5748269b591        2 months ago        608MB
    alpine              latest              3fd9065eaf02        2 months ago        4.15MB
    hello-world         latest              f2a91732366c        4 months ago        1.85kB


##### docker rmi

删除本地一个或多个镜像：

    docker rmi [OPTIONS] IMAGE [IMAGE...]

    Options:
    -f, --force      强制删除
        --no-prune   不移除该镜像的过程镜像，默认移除

当镜像正在被容器使用时，需先删除对应的容器，再删除镜像

    $ sudo docker rmi hello_docker
    Error response from daemon: conflict: unable to remove repository reference "hello_docker" (must force) - container 0581f2e3a913 is using its referenced image d68de571b8bb

或者使用 -f 选项强制删除镜像

    $ sudo docker rmi -f hello_docker
    Untagged: hello_docker:latest
    Deleted: sha256:d68de571b8bb337b0d0b43a088745d49d293044fbf0491568cd607b119ac560b
    Deleted: sha256:07768e3639e3895fe2dc11d28860b24694c201633e6dc2af3ae67e27d6e41fa8

##### docker tag

标记本地镜像，将其归入某一仓库：

    docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

    $ sudo docker tag jenkins:latest 172.29.20.54/devops/jenkins:latest

    $ sudo docker images | grep jenkins
    172.29.20.54/devops/jenkins   latest              7b210b6c238a        2 weeks ago         801MB
    jenkins                       latest              7b210b6c238a        2 weeks ago         801MB

##### docker history

查看指定镜像的创建历史：

    docker history [OPTIONS] IMAGE

    Options:
        --format string   
    -H, --human           以可读的格式打印镜像大小和日期，默认为true
        --no-trunc        显示完整的提交记录
    -q, --quiet           仅列出提交记录ID


    $ sudo docker history nginx
    IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
    7f70b30f2cc6        2 weeks ago         /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B                  
    <missing>           2 weeks ago         /bin/sh -c #(nop)  STOPSIGNAL [SIGTERM]         0B                  
    <missing>           2 weeks ago         /bin/sh -c #(nop)  EXPOSE 80/tcp                0B                  
    <missing>           2 weeks ago         /bin/sh -c ln -sf /dev/stdout /var/log/nginx…   22B                 
    <missing>           2 weeks ago         /bin/sh -c set -x  && apt-get update  && apt…   53.4MB              
    <missing>           2 weeks ago         /bin/sh -c #(nop)  ENV NJS_VERSION=1.13.10.0…   0B                  
    <missing>           2 weeks ago         /bin/sh -c #(nop)  ENV NGINX_VERSION=1.13.10…   0B                  
    <missing>           3 weeks ago         /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B                  
    <missing>           3 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B                  
    <missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:e3250bb9848f956bd…   55.3MB

##### docker save

将指定一个或多个镜像保存成tar归档文件：

    docker save [OPTIONS] IMAGE [IMAGE...]

    Options:
    -o, --output string   输出到文件

    $ sudo docker save -o nginx.tar nginx             
    $ ll
    -rw------- 1 root root 108M 4月   7 18:20 nginx.tar


##### docker import

从归档文件中创建镜像：

    docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]

    Options:
    -c, --change list      应用docker指令创建镜像
    -m, --message string   提交时的说明文字

    $ sudo docker import nginx.tar nginx:v1.0
    sha256:a04ebdfe23cc08d2c1511fb95af4d39f51937d26425f644a5b6d02b10e9f90e9
    $ sudo docker images nginx
    REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
    nginx               v1.0                a04ebdfe23cc        About a minute ago   113MB
    nginx               latest              c5c4e8fa2cf7        3 days ago           109MB


#### 容器生命周期管理

##### docker run

创建一个新的容器并运行一个命令：

    docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

    Options:
    -a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
    -d    后台运行容器，并返回容器ID
    -i    以交互模式运行容器，通常与 -t 同时使用
    -t    为容器重新分配一个伪输入终端，通常与 -i 同时使用
    -P    将容器的80端口映射到主机随机端口
    -p hostPort:containerPort   将容器的指定端口映射到主机指定端口
    -v    将主机文件系统挂载到容器中
    --name "container_name"    为容器指定一个名称
    --dns 8.8.8.8    指定容器使用的DNS服务器，默认和宿主一致
    --dns-search example.com     指定容器DNS搜索域名，默认和宿主一致
    -h "jenkins-01"    指定容器的hostname
    -e username="ritchie"   设置环境变量
    --env-file=[]    从指定文件读入环境变量
    --cpuset="0-2" or --cpuset="0,1,2"     绑定容器到指定CPU运行
    -m    设置容器使用内存最大值
    --net="bridge"     指定容器的网络连接类型，支持 bridge/host/none/container 四种类型
    --link=[]     添加链接到另一个容器
    --expose=[]     开放一个端口或一组端口


    $ sudo docker run --name contos_python_version centos python --version
    Python 2.7.5

    $ sudo docker run --name myblog -p 8888:80 -v /home/mingguilu/helloworld/blog/blog_mingguilu_com/public/:/usr/share/nginx/html/ -d nginx
    84da58fef876dd1b7d06190faedf549412c64e51b77306757abe31902727e089

    $ sudo docker run -it --name ubuntu_test -h srv-ubuntu-01 ubuntu          
    root@srv-ubuntu-01:/# hostname 
    srv-ubuntu-01
    root@srv-ubuntu-01:/# cat /etc/issue
    Ubuntu 16.04.4 LTS \n \l
    root@srv-ubuntu-01:/# exit


##### docker start|stop|restart

启动一个或多少已经被停止的容器：

    docker start [OPTIONS] CONTAINER [CONTAINER...]

    Options:
    -a, --attach               
        --detach-keys string   
    -i, --interactive          


停止一个或多个正在运行的容器：

    docker stop [OPTIONS] CONTAINER [CONTAINER...]

    Options:
    -t, --time int    停止容器前等待秒数(默认10秒)

重启一个或多个容器：

    docker restart [OPTIONS] CONTAINER [CONTAINER...]

    Options:
    -t, --time int    停止容器前等待秒数(默认10秒)


    $ sudo docker stop nginx_01
    nginx_01
    $ sudo docker start nginx_01
    nginx_01
    $ sudo docker restart nginx_01
    nginx_01

##### docker kill

杀掉一个或多个运行中的容器：

    docker kill [OPTIONS] CONTAINER [CONTAINER...]

    Options:
      -s, --signal string   向容器发送一个信号(默认是KILL)

    $ sudo docker kill nginx_01 blog
    nginx_01
    blog

##### docker rm

删除一个或多个容器：

    docker rm [OPTIONS] CONTAINER [CONTAINER...]

    Options:
      -f, --force     通过SIGKILL信号强制删除一个运行中的容器
      -l, --link      移除容器间的网络连接，而非容器本身
      -v, --volumes   删除与容器关联的卷

    $ sudo docker rm nginx_01
    Error response from daemon: You cannot remove a running container a3e3652e596307a45c31e93e33eee28756ea9bfc2ca78e3a41cde7638c65d4dd. Stop the container before attempting removal or force remove
    $ sudo docker rm -f nginx_01
    nginx_01

##### docker pause|unpause

暂停容器中的所有进程：

    docker pause CONTAINER [CONTAINER...]

恢复容器中的所有进程：

    docker unpause CONTAINER [CONTAINER...]

    $ sudo docker run --name nginx_01 -p 8080:80 -d nginx
    8392aaad023227a17dfa01a84782fa371b57995c5ad4ad457a8252b3ebcedc98

在浏览器中访问 http://localhost:8080 可访问到“Welcome to nginx ！”

    $ sudo docker pause nginx_01
    nginx_01

http://localhost:8080 访问失败

    $ sudo docker unpause nginx_01
    nginx_01

http://localhost:8080 恢复正常访问

##### docker create

创建一个新的容器但不启动它

    docker create [OPTIONS] IMAGE [COMMAND] [ARG...]

    Options：
      选项同 docker run

##### docker exec

在运行的容器中执行命令：

    docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

    Options:
      -d, --detach               在后台运行
          --detach-keys string   ？
      -e, --env list             ？
      -i, --interactive          即使没有附加也保持STDIN 打开
          --privileged           ？
      -t, --tty                  分配一个伪终端
      -u, --user string          用户名或用户ID (format: <name|uid>[:<group|gid>])
      -w, --workdir string       ？

    $ sudo docker exec -it nginx_01 /bin/bash
    root@8392aaad0232:/# ls /usr/share/nginx/html/
    50x.html  index.html
    root@8392aaad0232:/# 
    root@8392aaad0232:/# echo $HOSTNAME
    8392aaad0232
    root@8392aaad0232:/# echo $SHELL   
    /bin/bash
    root@8392aaad0232:/# cat /etc/issue
    Debian GNU/Linux 9 \n \l

#### 容器操作

##### docker ps

列出容器：

    docker ps [OPTIONS]

    Options:
    -a, --all             显示所有容器，包括未运行的(默认只显示正在运行的)
    -f, --filter filter   根据条件过滤显示的内容
        --format string   ？
    -n, --last int        列出最近创建的n个容器
    -l, --latest          显示最近创建的容器
        --no-trunc        显示完整的描述信息
    -q, --quiet           静默模式，只显示容器编号
    -s, --size            显示总的文件大小

    $ sudo docker ps -a 
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS                  NAMES
    8392aaad0232        nginx               "nginx -g 'daemon of…"   About an hour ago   Up About an hour               0.0.0.0:8080->80/tcp   nginx_01
    ab11f19d41e2        centos              "python --version"       2 hours ago         Exited (0) About an hour ago                          contos_python_version

##### docker inspect

获取容器/镜像的元数据:

    docker inspect [OPTIONS] NAME|ID [NAME|ID...]

    Options:
      -f, --format string   指定返回值的模板文件
      -s, --size            显示总的文件大小
          --type string     为指定类型返回JSON

    $ sudo docker inspect nginx_01
    [
        {
            "Id": "8392aaad023227a17dfa01a84782fa371b57995c5ad4ad457a8252b3ebcedc98",
            "Created": "2018-04-07T15:42:23.457108312Z",
            "Path": "nginx",
            "Args": [
                "-g",
                "daemon off;"
            ],
            "State": {
                "Status": "running",
                "Running": true,
                "Paused": false,
                "Restarting": false,
                "OOMKilled": false,
                "Dead": false,
                "Pid": 20734,
                "ExitCode": 0,
                "Error": "",
                "StartedAt": "2018-04-07T15:42:25.014574543Z",
                "FinishedAt": "0001-01-01T00:00:00Z"
            },
            "Image": "sha256:c5c4e8fa2cf7d87545ed017b60a4b71e047e26c4ebc71eb1709d9e5289f9176f",
      ......
                "MacAddress": "02:42:ac:11:00:02",
                "Networks": {
                    "bridge": {
                        "IPAMConfig": null,
                        "Links": null,
                        "Aliases": null,
                        "NetworkID": "676ba707a1c7a0d1ad85ba67ec496848c267b1e89f18bccbabd0036534daf0e9",
                        "EndpointID": "d1112dc2ede132a1447b4265402995328004c9e808ea8bcb47a18d958046ebb7",
                        "Gateway": "172.17.0.1",
                        "IPAddress": "172.17.0.2",
                        "IPPrefixLen": 16,
                        "IPv6Gateway": "",
                        "GlobalIPv6Address": "",
                        "GlobalIPv6PrefixLen": 0,
                        "MacAddress": "02:42:ac:11:00:02",
                        "DriverOpts": null
                    }
                }
            }
        }
    ]

获取正在运行的容器nginx_01的IP地址：

    $ sudo docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nginx_01
    172.17.0.2

##### docker top

查看容器中运行的进程信息，支持ps命令参数:

    docker top CONTAINER [ps OPTIONS]

    $ sudo docker top nginx_01
    UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
    root                20734               20716               0                   4月07                ?                   00:00:00            nginx: master process nginx -g daemon off;
    101                 20778               20734               0                   4月07                ?                   00:00:00            nginx: worker process



##### docker events

从服务器获取实时事件：

    docker events [OPTIONS]

    Options:
      -f, --filter filter   根据条件过滤事件
          --format string   
          --since string    从指定的时间戳后显示所有事件
          --until string    流水时间显示到指定的时间为止
      
    $ sudo docker events -f "image"="nginx" --since="1467302400"  
    2018-04-07T22:35:43.908389121+08:00 container create 7054f2265ba8fc5852666b4ecf53b4e3b670157476f86c99c83136a2f8d382ea (image=nginx, maintainer=NGINX Docker Maintainers <docker-maint@nginx.com>, name=nginx_01)
    2018-04-07T22:35:43.909712905+08:00 container attach 7054f2265ba8fc5852666b4ecf53b4e3b670157476f86c99c83136a2f8d382ea (image=nginx, maintainer=NGINX Docker Maintainers <docker-maint@nginx.com>, name=nginx_01)
    2018-04-07T22:35:45.509956611+08:00 container start 7054f2265ba8fc5852666b4ecf53b4e3b670157476f86c99c83136a2f8d382ea (image=nginx, maintainer=NGINX Docker Maintainers <docker-maint@nginx.com>, name=nginx_01)
    ......

##### docker logs

获取容器的日志：

    docker logs [OPTIONS] CONTAINER

    Options:
          --details        显示额外的详细信息
      -f, --follow         跟踪日志输出
          --since string   显示某个开始时间的所有日志(e.g. 2013-01-02T13:23:37)
                          or relative (e.g. 42m for 42 minutes)
          --tail string    仅列出最新N条容器日志(默认全部)
      -t, --timestamps     显示时间戳
          --until string   显示到指定的时间为止(e.g.
                          2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)

    $ sudo docker logs -f nginx_01
    172.17.0.1 - - [07/Apr/2018:15:42:36 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36" "-"
    172.17.0.1 - - [07/Apr/2018:15:43:26 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36" "-"
    172.17.0.1 - - [07/Apr/2018:15:43:26 +0000] "GET / HTTP/1.1" 200 612 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36" "-"
    172.17.0.1 - - [07/Apr/2018:15:43:26 +0000] "GET / HTTP/1.1" 200 612 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36" "-"
    ......

    $ sudo docker logs --tail 3 nginx_01
    172.17.0.1 - - [07/Apr/2018:16:46:07 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36" "-"
    2018/04/07 16:46:12 [error] 8#8: *14 open() "/usr/share/nginx/html/index.jsp" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /index.jsp HTTP/1.1", host: "localhost:8080"
    172.17.0.1 - - [07/Apr/2018:16:46:12 +0000] "GET /index.jsp HTTP/1.1" 404 572 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36" "-"

##### docker wait

阻塞运行直到容器停止，然后打印出它的退出代码

    docker wait CONTAINER [CONTAINER...]

先在一个终端运行docker wait ...

  $ sudo docker wait nginx_01

然后在另一个终端停止容器

  $ sudo docker stop nginx_01
  nginx_01

在第一个终端看到推出代码 0

  $ sudo docker wait nginx_01
  0

##### docker export

将容器作为打包成一个tar归档文件：

    docker export [OPTIONS] CONTAINER

    Export a container's filesystem as a tar archive

    Options:
      -o, --output string   输入到一个文件

    $ sudo docker export -o nginx-01.tar nginx_01
    $ ll
    -rw------- 1 root root 106M 4月   8 01:17 nginx-01.tar

注意： docker save 是将镜像打包成文件，docker export 是将容器打包成文件

##### docker port

列出指定的容器的端口映射：

    docker port CONTAINER [PRIVATE_PORT[/PROTO]]

    $ sudo docker port blog.mingguilu.com
    80/tcp -> 0.0.0.0:8888

#### 容器rootfs命令

##### docker commit

从容器创建一个新的镜像：

    docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

    Options:
      -a, --author string    作者 (e.g., "John Hannibal Smith <hannibal@a-team.com>")
      -c, --change list      使用Dockerfile指令来创建镜像
      -m, --message string   提交时的说明文字
      -p, --pause            在提交时，将容器暂停(默认会暂停)

    $ sudo docker commit -a "Minguilu <luminggui0214@gmail.com>" -m "MingguiLu's blog page" blog.mingguilu.com  myblog:v1
    sha256:793f080e94e00249b6c7b1c62c0ccdd2818b1c14f986fdcd7c35ab45064e056d
    $ sudo docker images myblog:v1
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    myblog              v1                  793f080e94e0        9 seconds ago       109MB

##### docker cp

用户容器与主机之间的数据拷贝：

    docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
    docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH

    Options:
      -a, --archive       归档模式(拷贝全被的用户或用户ID的信息)
      -L, --follow-link   保持源目标中的链接

    $ sudo docker cp helloworld/html/index.html blog.mingguilu.com:/usr/share/nginx/html/


##### docker diff

检查容器里文件结构的更改：

    docker diff CONTAINER

    $ sudo docker diff blog.mingguilu.com
    C /run
    A /run/nginx.pid
    C /var
    C /var/cache
    C /var/cache/nginx
    A /var/cache/nginx/client_temp
    A /var/cache/nginx/fastcgi_temp
    A /var/cache/nginx/proxy_temp
    A /var/cache/nginx/scgi_temp
    A /var/cache/nginx/uwsgi_temp


---

#### 参考文档

* 《Docker 命令大全》 [http://www.runoob.com/docker/docker-command-manual.html](http://www.runoob.com/docker/docker-command-manual.html)




