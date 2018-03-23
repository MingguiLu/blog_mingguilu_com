---
title: CentOS7使用GlusterFS实现Harbor镜像仓库的分布式存储
description: ''
photos: /images/photos/gluster_ceph.jpg
date: 2018-03-12 20:51:06
tags:
- CentOS
- Docker
- Harbor
- GlusterFS
categories: 技术分享
---

#### 实验环境

##### Hostname、IP

* Docker+Harbor     os：CentOS7.2    hostname：centos7-01         ip：72.29.20.54 
* GlusterFS节点1      os：CentOS7.2    hostname：gluster-node-1     ip：172.29.20.57
* GlusterFS节点2      os：CentOS7.2    hostname：gluster-node-2     ip：172.29.20.59

##### 磁盘信息

* GlusterFS节点1   /dev/sda：20G 安装系统   /dev/sdb：20G GlusterFS存储
* GlusterFS节点2   /dev/sda：20G 安装系统   /dev/sdb：20G GlusterFS存储

#### 格式化并挂载磁盘

同时在gluster-node-1和gluster-node-2上进行

##### 查看磁盘信息

    [mingguilu@gluster-node-1 ~]$ sudo fdisk -l

    磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
    Units = 扇区 of 1 * 512 = 512 bytes
    扇区大小(逻辑/物理)：512 字节 / 512 字节
    I/O 大小(最小/最佳)：512 字节 / 512 字节


    磁盘 /dev/sda：21.5 GB, 21474836480 字节，41943040 个扇区
    Units = 扇区 of 1 * 512 = 512 bytes
    扇区大小(逻辑/物理)：512 字节 / 512 字节
    I/O 大小(最小/最佳)：512 字节 / 512 字节
    磁盘标签类型：dos
    磁盘标识符：0x000a1bd9

    设备 Boot      Start         End      Blocks   Id  System
    /dev/sda1   *        2048     2099199     1048576   83  Linux
    /dev/sda2         2099200    41943039    19921920   8e  Linux LVM

    磁盘 /dev/mapper/centos-root：18.2 GB, 18249416704 字节，35643392 个扇区
    Units = 扇区 of 1 * 512 = 512 bytes
    扇区大小(逻辑/物理)：512 字节 / 512 字节
    I/O 大小(最小/最佳)：512 字节 / 512 字节


    磁盘 /dev/mapper/centos-swap：2147 MB, 2147483648 字节，4194304 个扇区
    Units = 扇区 of 1 * 512 = 512 bytes
    扇区大小(逻辑/物理)：512 字节 / 512 字节
    I/O 大小(最小/最佳)：512 字节 / 512 字节

##### 磁盘分区

    [root@gluster-node-1 mingguilu]# fdisk /dev/sdb 
    欢迎使用 fdisk (util-linux 2.23.2)。

    更改将停留在内存中，直到您决定将更改写入磁盘。
    使用写入命令前请三思。

    Device does not contain a recognized partition table
    使用磁盘标识符 0x896a8f0e 创建新的 DOS 磁盘标签。

    命令(输入 m 获取帮助)：m
    命令操作
    a   toggle a bootable flag
    b   edit bsd disklabel
    c   toggle the dos compatibility flag
    d   delete a partition
    g   create a new empty GPT partition table
    G   create an IRIX (SGI) partition table
    l   list known partition types
    m   print this menu
    n   add a new partition
    o   create a new empty DOS partition table
    p   print the partition table
    q   quit without saving changes
    s   create a new empty Sun disklabel
    t   change a partition's system id
    u   change display/entry units
    v   verify the partition table
    w   write table to disk and exit
    x   extra functionality (experts only)

    命令(输入 m 获取帮助)：n
    Partition type:
    p   primary (0 primary, 0 extended, 4 free)
    e   extended
    Select (default p): p
    分区号 (1-4，默认 1)：
    起始 扇区 (2048-41943039，默认为 2048)：
    将使用默认值 2048
    Last 扇区, +扇区 or +size{K,M,G} (2048-41943039，默认为 41943039)：
    将使用默认值 41943039
    分区 1 已设置为 Linux 类型，大小设为 20 GiB

    命令(输入 m 获取帮助)：w
    The partition table has been altered!

    Calling ioctl() to re-read partition table.
    正在同步磁盘。

    [root@gluster-node-2 mingguilu]# fdisk -l | grep sdb
    磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
    /dev/sdb1            2048    41943039    20970496   83  Linux


##### 磁盘格式化

    [root@gluster-node-1 mingguilu]# mkfs.xfs -i size=512 /dev/sdb1
    meta-data=/dev/sdb1              isize=512    agcount=4, agsize=1310656 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0, sparse=0
    data     =                       bsize=4096   blocks=5242624, imaxpct=25
            =                       sunit=0      swidth=0 blks
    naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
    log      =internal log           bsize=4096   blocks=2560, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0

##### 创建存储挂载点目录

    [root@gluster-node-1 mingguilu]# mkdir -p /data/brick1

##### 设置开机自动载

    [root@gluster-node-1 mingguilu]# echo '/dev/sdb1 /data/brick1 xfs defaults 1 2' >> /etc/fstab

##### 挂载

    [root@gluster-node-1 ~]# mount -a
    [root@gluster-node-1 ~]# mount | grep sdb1
    /dev/sdb1 on /data/brick1 type xfs (rw,relatime,seclabel,attr2,inode64,noquota)

#### 安装GlusterFS 

##### 添加GlusterFS安装源

    [root@gluster-node-1 ~]# yum install centos-release-gluster

##### 安装GlusterFS

    [root@gluter-node-1 ~]# yum install -y glusterfs-server

##### 启动Gluster

    [root@gluster-node-1 ~]# systemctl start glusterd

##### 开机自启动

    [root@gluster-node-1 ~]# systemctl enable glusterd
Created symlink from /etc/systemd/system/multi-user.target.wants/glusterd.service to /usr/lib/systemd/system/glusterd.service.

##### 查看状态

    [root@gluster-node-1 ~]# systemctl status glusterd
    ● glusterd.service - GlusterFS, a clustered file-system server
       Loaded: loaded (/usr/lib/systemd/system/glusterd.service; enabled; vendor preset: disabled)
       Active: active (running) since 一 2018-03-12 16:41:40 CST; 18s ago
     Main PID: 8894 (glusterd)
       CGroup: /system.slice/glusterd.service
               └─8894 /usr/sbin/glusterd -p /var/run/glusterd.pid --log-level INFO
    
    3月 12 16:41:40 gluster-node-1 systemd[1]: Starting GlusterFS, a clustered file....
    3月 12 16:41:40 gluster-node-1 systemd[1]: Started GlusterFS, a clustered file-....
    Hint: Some lines were ellipsized, use -l to show in full.

##### 停止GlusterFS

    [root@gluster-node-1 ~]# systemctl stop glusterd

#### 添加节点

##### gluster-node-1上

    [root@gluster-node-1 mingguilu]# gluster peer probe 172.29.20.59
    peer probe: success.

##### gluster-node-2上

    [root@gluster-node-2 ~]# gluster peer probe 172.29.20.57
    peer probe: success. 

##### 检查节点状态

    [root@gluster-node-1 mingguilu]# gluster peer status
    Number of Peers: 1
    
    Hostname: 172.29.20.59
    Uuid: c786d332-1647-4b1b-9640-adbf7b80162d
    State: Peer in Cluster (Connected)
    
    
    [root@gluster-node-2 ~]# gluster peer status
    Number of Peers: 1
    
    Hostname: gluster-node-1
    Uuid: a3d5a5bb-d60d-411d-bafb-0d3439f211e0
    State: Peer in Cluster (Connected)
    Other names:
    172.29.20.57

#### 创建Volume

##### 创建volume挂载点

    [root@gluster-node-1 mingguilu]# mkdir -p /data/brick1/gv0
    
    [root@gluster-node-2 mingguilu]# mkdir -p /data/brick1/gv0


##### 创建volume

在随意一台上：

    [root@gluster-node-1 mingguilu]# gluster volume create gv0 replica 2 gluster-node-1:/data/brick1/gv0 gluster-node-2:/data/brick1/gv0
    Replica 2 volumes are prone to split-brain. Use Arbiter or Replica 3 to avoid this. See: http://docs.gluster.org/en/latest/Administrator%20Guide/Split%20brain%20and%20ways%20to%20deal%20with%20it/.
    Do you still want to continue?
     (y/n) y
    volume create: gv0: success: please start the volume to access data

##### 启动volume

    [root@gluster-node-1 mingguilu]# gluster volume create gv0 replica 2 gluster-node-1:/data/brick1/gv0 gluster-node-2:/data/brick1/gv0
    Replica 2 volumes are prone to split-brain. Use Arbiter or Replica 3 to avoid this. See: http://docs.gluster.org/en/latest/Administrator%20Guide/Split%20brain%20and%20ways%20to%20deal%20with%20it/.
    Do you still want to continue?
     (y/n) y
    volume create: gv0: success: please start the volume to access data

##### 查看volume状态

    [root@gluster-node-1 mingguilu]# gluster volume info
     
    Volume Name: gv0
    Type: Replicate
    Volume ID: df3bf85e-1e39-43ae-9e7d-d857b4fce7a5
    Status: Started
    Snapshot Count: 0
    Number of Bricks: 1 x 2 = 2
    Transport-type: tcp
    Bricks:
    Brick1: gluster-node-1:/data/brick1/gv0
    Brick2: gluster-node-2:/data/brick1/gv0
    Options Reconfigured:
    transport.address-family: inet
    nfs.disable: on
    performance.client-io-threads: off

    [root@gluster-node-2 ~]# gluster volume info
     
    Volume Name: gv0
    Type: Replicate
    Volume ID: df3bf85e-1e39-43ae-9e7d-d857b4fce7a5
    Status: Started
    Snapshot Count: 0
    Number of Bricks: 1 x 2 = 2
    Transport-type: tcp
    Bricks:
    Brick1: gluster-node-1:/data/brick1/gv0
    Brick2: gluster-node-2:/data/brick1/gv0
    Options Reconfigured:
    transport.address-family: inet
    nfs.disable: on
    performance.client-io-threads: off

#### 将Harbor镜像仓库挂载到GlusterFS

##### 安装GlusterFS和Glusterfs-client


    yum install -y glusterfs glusterfs-client

##### 停止harbor

    [root@centos7-01 harbor]# docker-compose stop
    Stopping nginx              ... done
    Stopping harbor-jobservice  ... done
    Stopping harbor-ui          ... done
    Stopping harbor-db          ... done
    Stopping harbor-adminserver ... done
    Stopping registry           ... done
    Stopping harbor-log         ... done

##### 挂载

    [root@centos7-01 harbor]# mount -t glusterfs gluster-node-1:gv0 /data/registry/docker/registry/v2/repositories/


<!--more-->