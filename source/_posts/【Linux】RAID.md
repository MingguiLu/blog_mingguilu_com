---
title: 【Linux】RAID
tags:
  - linux
  - RAID
categories: 点滴随笔
photos: /images/photos/linux.jpg
date: 2018-08-22 09:27:50
description:
---

更多的RAID知识参考维基：[磁盘阵列RAID](https://zh.wikipedia.org/wiki/RAID)

mdadm命令用于管理软件RAID磁盘阵列，CentOS 7.4默认未安装，需要自行安装｀yum install -y mdadm｀

语法：mdadm [模式] <RAID设备名称> [选项] [成员设备名称]

| 参数 | 作用 |
| :---: | :---: |
| -C | 创建, -a yes表示自动创建设备文件 |
| -v | 显示过程 |
| -a {yes/no} | 是否自动（auto）创建RAID的设备文件 |
| -n | 指定设备数量 |
| -l | 指定RAID级别 |
| -x | 添加热备份盘 |
| -a | 添加设备 |
| -f | 标记为损坏的设备 |
| -r | 移除设备 |
| -Q | 查看摘要信息 |
| -D | 查看详细信息 |
| -S | 停止RAID磁盘阵列 |

RAID阵列及磁盘的状态信息:

| 状态 | 级别 | 含义 |
| :---: | :---: | :---: |
| clean | RAID阵列 | 正常状态 |
| degraded | RAID阵列 | 降级状态，RAID阵列中有磁盘存在故障，但阵列还可正常使用 |
| recovering | RAID阵列 | 恢复状态，RAID阵列中加入了新的磁盘，正在同步数据 |
| active sync | 磁盘 | 正常同步 |
| faulty | 磁盘 | 磁盘故障 |
| spare | 磁盘 | 备用磁盘 |
| spare rebuilding | 磁盘 | 顶替上了故障磁盘，正在修复数据 |

<!--more-->

#### RAID 0

将２块以上磁盘并联起来，成为一个大容量的磁盘。在存放数据时，分段后分散存储在这些磁盘中，因为读写时都可以并行处理，所以在所有的级别中，RAID 0的速度是最快的，但不具备数据备容错能力

![](/images/20180822/20180822_RAID0.png)

#### RAID 1

２块以上的磁盘进行绑定互相做镜像，在写入数据时同时写入到多块磁盘上，某一块发生故障后，一般会立即自动以热交换的方式来恢复数据的正常使用。理论上读取速度与RAID 0相同，写入速度会有微小的降低，磁盘利用率所有RAID中最低级别的；如果两个不同大小的磁盘做RAID 1,可用空间为最小的磁盘，较大的磁盘多余空间可以分区来使用，不造成浪费

![](/images/20180822/20180822_RAID1.png)

#### RAID 5

可以理解为是RAID 0和RAID 1的折衷,是一种储存性能、数据安全和存储成本兼顾的存储解决方案，至少需要３块磁盘。RAID 5不是对存储的数据进行备份，而是把数据和相对应的奇偶校验信息存储到组成RAID5的各个磁盘上，并且奇偶校验信息和相对应的数据分别存储于不同的磁盘上。当RAID5的一个磁盘数据发生损坏后，可以利用剩下的数据和相应的奇偶校验信息去恢复被损坏的数据。理论上读取速度与RAID 0差不多，写入速度相对单独写入一块磁盘的速度略慢

![](/images/20180822/20180822_RAID5.png)

##### 部署RAID 5

###### 查看磁盘设备

    # fdisk -l|grep sd
    磁盘 /dev/sda：107.4 GB, 107374182400 字节，209715200 个扇区
    /dev/sda1   *        2048     2099199     1048576   83  Linux
    /dev/sda2         2099200   209715199   103808000   8e  Linux LVM
    磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
    磁盘 /dev/sdc：21.5 GB, 21474836480 字节，41943040 个扇区
    磁盘 /dev/sdd：21.5 GB, 21474836480 字节，41943040 个扇区
    磁盘 /dev/sde：21.5 GB, 21474836480 字节，41943040 个扇区

###### 创建RAID 5 +热备份盘

    # mdadm -Cv /dev/md5 -a yes -n 3 -l 5 /dev/sd{b,c,d} -x 1 /dev/sde
    mdadm: layout defaults to left-symmetric
    mdadm: layout defaults to left-symmetric
    mdadm: chunk size defaults to 512K
    mdadm: size set to 20954112K
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md5 started.

###### ###### 格式化RAID磁盘阵列为xfs格式

    # mkfs.xfs /dev/md5 
    meta-data=/dev/md5               isize=512    agcount=16, agsize=654720 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0, sparse=0
    data     =                       bsize=4096   blocks=10475520, imaxpct=25
            =                       sunit=128    swidth=256 blks
    naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
    log      =internal log           bsize=4096   blocks=5120, version=2
            =                       sectsz=512   sunit=8 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0

###### 创建挂载点并挂载/dev/md5

    # mkdir /data
    # mount /dev/md5 /data
    # df -h |grep data
    文件系统      容量  已用  可用 　已用% 　挂载点
    /dev/md5    40G   33M   40G   1%    /data

###### 挂载信息写入/etc/fstab，使其开机自动挂载

    # echo "/dev/md5 /data xfs defaults 0 0" >> /etc/fstab 
    # tail -1 /etc/fstab 
    /dev/md5 /data xfs defaults 0 0

###### 查看/dev/md5磁盘阵列信息

    # mdadm -D /dev/md5 
    /dev/md5:
              Version : 1.2
        Creation Time : Fri Aug 24 19:56:06 2018
            Raid Level : raid5
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 3
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 20:03:02 2018
                State : clean 　　　　　　＃软RAID状态正常
      　Active Devices : 3              ＃以激活的设备３块
      Working Devices : 4　　　　　　　　　＃可用的设备４块
        Failed Devices : 0
        Spare Devices : 1　　　　　　　　　＃热备份设备１块

                Layout : left-symmetric
            Chunk Size : 512K

    Consistency Policy : resync

                  Name : ops-saltmaster-1:5  (local to host ops-saltmaster-1)
                  UUID : 10a5438a:b39bc3fa:63a47ad3:adf16917
                Events : 22

        Number   Major   Minor   RaidDevice State
          0       8       16        0      active sync   /dev/sdb
          1       8       32        1      active sync   /dev/sdc
          4       8       48        2      active sync   /dev/sdd

          3       8       64        -      spare   /dev/sde　　　　　＃处于备用状态

###### 磁盘阵列故障，备份盘启用

使用 -f 参数模拟/dev/sdc磁盘故障，备份盘/dev/sde立即自动顶替上了，并自动修复数据

    # mdadm /dev/md5 -f /dev/sdc
    mdadm: set /dev/sdc faulty in /dev/md5
    # mdadm -D /dev/md5 
    /dev/md5:
          　　Version : 1.2
      　Creation Time : Fri Aug 24 19:56:06 2018
        　Raid Level : raid5
          Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
        Raid Devices : 3
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 20:26:40 2018
                State : clean, degraded, recovering 　＃出现降级和修复状态
        Active Devices : 2　　　　　　　　　　　　　　　　　＃激活的设备只有２块了，因为此时/dev/sde还处于spare rebuilding状态
      Working Devices : 3　　　　　　　　　　　　　　　　　＃可用的设备三块
        Failed Devices : 1  　　　　　　　　　　　　　　　＃故障设备１块
        Spare Devices : 1　　　　　　　　　　　　　　　　　＃备用设备１块，因为此时/dev/sde还处于spare rebuilding状态

                Layout : left-symmetric
            Chunk Size : 512K

    Consistency Policy : resync

        Rebuild Status : 1% complete                 #数据修复的完成度

                  Name : ops-saltmaster-1:5  (local to host ops-saltmaster-1)
                  UUID : 10a5438a:b39bc3fa:63a47ad3:adf16917
                Events : 24

        Number   Major   Minor   RaidDevice State
          0       8       16        0      active sync   /dev/sdb
          3       8       64        1      spare rebuilding   /dev/sde　　　#热备份盘已顶替上，并开始修复数据
          4       8       48        2      active sync   /dev/sdd

          1       8       32        -      faulty   /dev/sdc　　　　　　　　　＃状态变为faulty，表示故障

###### 数据修复完成后再查看磁盘阵列信息

    # mdadm -D /dev/md5 
    /dev/md5:
              Version : 1.2
        Creation Time : Fri Aug 24 19:56:06 2018
            Raid Level : raid5
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 3
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 20:40:09 2018
                State : clean 　　　　　　　　　　　　　
        Active Devices : 3　　　　　　　　　　　　　　　＃激活的设备恢复到３块
      Working Devices : 3　　　　　　　　　　　　　　　　
        Failed Devices : 1
        Spare Devices : 0　　　　　　　　　　　　　　　＃备用设备为０块了

                Layout : left-symmetric
            Chunk Size : 512K

    Consistency Policy : resync

                  Name : ops-saltmaster-1:5  (local to host ops-saltmaster-1)
                  UUID : 10a5438a:b39bc3fa:63a47ad3:adf16917
                Events : 41

        Number   Major   Minor   RaidDevice State
          0       8       16        0      active sync   /dev/sdb
          3       8       64        1      active sync   /dev/sde
          4       8       48        2      active sync   /dev/sdd

          1       8       32        -      faulty   /dev/sdc

 
#### RAID 10/01

RAID 10/01是RAID 1+RAID 0的一个组合体，**生产环境中主要使用RAID 10**

RAID 10是先镜射再分区数据，再将所有磁盘分为两组，视为是RAID 0的最低组合，然后将这两组各自视为RAID 1运作。

![](/images/20180822/20180822_RAID10.png)

RAID 01则是跟RAID 10的程序相反，是先分区再将数据镜射到两组磁盘。它将所有的磁盘分为两组，变成RAID 1的最低组合，而将两组磁盘各自视为RAID 0运作。

![](/images/20180822/20180822_RAID01.png)

当RAID 10有一个磁盘受损，其余磁盘会继续运作，理论上讲，只要坏的不是同一组中的所有磁盘，那么最多可以损坏50%的磁盘而不丢失数据，。RAID 01只要有一个磁盘受损，同组RAID 0的所有磁盘都会停止运作，只剩下其他组的磁盘运作，可靠性较低。如果以六个磁盘建RAID 01，镜射再用三个建RAID 0，那么坏一个磁盘便会有三个磁盘离线。因此，RAID 10远较RAID 01常用，零售主板绝大部分支持RAID 0/1/5/10，但不支持RAID 01。

##### 部署RAID 10

###### 查看磁盘设备

    # fdisk -l | grep sd
    磁盘 /dev/sda：107.4 GB, 107374182400 字节，209715200 个扇区
    /dev/sda1   *        2048     2099199     1048576   83  Linux
    /dev/sda2         2099200   209715199   103808000   8e  Linux LVM
    磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
    磁盘 /dev/sdc：21.5 GB, 21474836480 字节，41943040 个扇区
    磁盘 /dev/sdd：21.5 GB, 21474836480 字节，41943040 个扇区
    磁盘 /dev/sde：21.5 GB, 21474836480 字节，41943040 个扇区
    磁盘 /dev/sdf：21.5 GB, 21474836480 字节，41943040 个扇区

###### 创建RAID 10

将/dev/sdb、/dev/sdc、/dev/sdd、/dev/sde部署为RAID 10磁盘阵列

    # mdadm -Cv /dev/md10 -a yes -n 4 -l 10 /dev/sd{b,c,d,e} 
    mdadm: layout defaults to n2
    mdadm: layout defaults to n2
    mdadm: chunk size defaults to 512K
    mdadm: size set to 20954112K
    mdadm: Defaulting to version 1.2 metadata
    mdadm: array /dev/md10 started.

如果创建RAID 10磁盘阵列的同时加入热备份盘，需要增加 -x 参数，当然先创建磁盘阵列再添加热备份盘也是可以的，后面也会演示到

    # mdadm -Cv /dev/md10 -a yes -n 4 -l 10 /dev/sd{b,c,d,e} -x 1 /dev/sdf

###### 格式化RAID磁盘阵列为xfs格式

    # mkfs.xfs /dev/md10
    meta-data=/dev/md10              isize=512    agcount=16, agsize=654720 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0, sparse=0
    data     =                       bsize=4096   blocks=10475520, imaxpct=25
            =                       sunit=128    swidth=256 blks
    naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
    log      =internal log           bsize=4096   blocks=5120, version=2
            =                       sectsz=512   sunit=8 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0

###### 创建挂载点并挂载/dev/md10

    # mkdir /data
    # mount /dev/md10 /data/
    # df -h |grep data
    文件系统      容量  已用  可用 　已用% 　挂载点
    /dev/md10   40G   33M   40G   1% 　　/data
  
    # echo `seq 999` > /data/test.txt 
  
###### 查看/dev/md10磁盘阵列信息

    # mdadm -D /dev/md10 
    /dev/md10:
              Version : 1.2
        Creation Time : Fri Aug 24 15:41:15 2018
            Raid Level : raid10
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 4
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 15:45:15 2018
                State : active 
        Active Devices : 4
      Working Devices : 4
        Failed Devices : 0
        Spare Devices : 0

                Layout : near=2
            Chunk Size : 512K

    Consistency Policy : resync

                  Name : ops-saltmaster-1:10  (local to host ops-saltmaster-1)
                  UUID : 8d165aec:c19ebcfe:d4f87598:d6b74e83
                Events : 18

        Number   Major   Minor   RaidDevice State
          0       8       16        0      active sync set-A   /dev/sdb
          1       8       32        1      active sync set-B   /dev/sdc
          2       8       48        2      active sync set-A   /dev/sdd
          3       8       64        3      active sync set-B   /dev/sde

###### 挂载信息写入/etc/fstab，使其开机自动挂载

    # echo "/dev/md10 /data xfs defaults 0 0" >> /etc/fstab
    # tail -1 /etc/fstab
    /dev/md10 /data xfs defaults 0 0

###### 磁盘阵列损坏

####### 模拟/dev/sdb磁盘故障

    # mdadm /dev/md10 -f /dev/sdb
    mdadm: set /dev/sdb faulty in /dev/md10

    # mdadm -D /dev/md10
    /dev/md10:
              Version : 1.2
        Creation Time : Fri Aug 24 15:41:15 2018
            Raid Level : raid10
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 4
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 15:59:58 2018
                State : active, degraded          ＃出现了降级的状态
        Active Devices : 3　　　                   ＃激活的设备只有３块了
      Working Devices : 3                         ＃可用的设备也是３块
        Failed Devices : 1　　　　　　　　　　       ＃故障设备１块
        Spare Devices : 0

                Layout : near=2
            Chunk Size : 512K

    Consistency Policy : resync

                  Name : ops-saltmaster-1:10  (local to host ops-saltmaster-1)
                  UUID : 8d165aec:c19ebcfe:d4f87598:d6b74e83
                Events : 20

        Number   Major   Minor   RaidDevice State
          -       0        0        0      removed
          1       8       32        1      active sync set-B   /dev/sdc
          2       8       48        2      active sync set-A   /dev/sdd
          3       8       64        3      active sync set-B   /dev/sde

          0       8       16        -      faulty   /dev/sdb            　＃状态变为faulty，表示故障

    # cat /proc/mdstat 
    Personalities : [raid10] 
    md10 : active raid10 sde[3] sdd[2] sdc[1] sdb[0](F)
          41908224 blocks super 1.2 512K chunks 2 near-copies [4/3] [_UUU]
          
    unused devices: <none>

####### 将故障磁盘移除

生产中从RAID磁盘阵列中移除故障磁盘后，就可以关机把故障磁盘取下来，带维修好再安装回去或更换新磁盘上去，

    # mdadm /dev/md10 -r /dev/sdb
    mdadm: hot removed /dev/sdb from /dev/md10
    
    # mdadm -D /dev/md10 
    /dev/md10:
              Version : 1.2
        Creation Time : Fri Aug 24 15:41:15 2018
            Raid Level : raid10
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 4
        Total Devices : 3
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 16:28:19 2018
                State : active, degraded 　　
        Active Devices : 3
      Working Devices : 3
        Failed Devices : 0              ＃故障磁盘已移除
        Spare Devices : 0

                Layout : near=2
            Chunk Size : 512K

    Consistency Policy : resync

                  Name : ops-saltmaster-1:10  (local to host ops-saltmaster-1)
                  UUID : 8d165aec:c19ebcfe:d4f87598:d6b74e83
                Events : 43

        Number   Major   Minor   RaidDevice State
          -       0        0        0      removed  
          1       8       32        1      active sync set-B   /dev/sdc
          2       8       48        2      active sync set-A   /dev/sdd
          3       8       64        3      active sync set-B   /dev/sde

###### 修复RAID磁盘阵列

假设更换了新磁盘(设备名称还是/dev/sdb)并开机了，现在把新磁盘添加到RAID磁盘阵列中，并等待修复数据

    # umount /data
    # mdadm /dev/md10 -a /dev/sdb
    mdadm: added /dev/sdb
    # mdadm -D /dev/md10 
    /dev/md10:
              Version : 1.2
        Creation Time : Fri Aug 24 15:41:15 2018
            Raid Level : raid10
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 4
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 16:49:15 2018
                State : active, degraded, recovering 　　＃出现修复状态
        Active Devices : 3
      Working Devices : 4
        Failed Devices : 0
        Spare Devices : 1

                Layout : near=2
            Chunk Size : 512K

    Consistency Policy : resync

        Rebuild Status : 2% complete                    #数据修复的完成度

                  Name : ops-saltmaster-1:10  (local to host ops-saltmaster-1)
                  UUID : 8d165aec:c19ebcfe:d4f87598:d6b74e83
                Events : 45

        Number   Major   Minor   RaidDevice State
          4       8       16        0      spare rebuilding   /dev/sdb　　　#添加成功，状态为修复数据状态
          1       8       32        1      active sync set-B   /dev/sdc
          2       8       48        2      active sync set-A   /dev/sdd
          3       8       64        3      active sync set-B   /dev/sde

数据修复完成后重新挂载目录

    # mdadm -D /dev/md10 
    /dev/md10:
              Version : 1.2
        Creation Time : Fri Aug 24 15:41:15 2018
            Raid Level : raid10
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 4
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 16:52:37 2018
                State : active                    　#状态变为活动
        Active Devices : 4                          #激活磁盘设备恢复为４块
      Working Devices : 4　　　　　　　　　　　　　　　　＃可用盘设备恢复为４块
        Failed Devices : 0
        Spare Devices : 0

                Layout : near=2
            Chunk Size : 512K

    Consistency Policy : resync

                  Name : ops-saltmaster-1:10  (local to host ops-saltmaster-1)
                  UUID : 8d165aec:c19ebcfe:d4f87598:d6b74e83
                Events : 62

        Number   Major   Minor   RaidDevice State
          4       8       16        0      active sync set-A   /dev/sdb　　　#状态恢复活动同步
          1       8       32        1      active sync set-B   /dev/sdc
          2       8       48        2      active sync set-A   /dev/sdd
          3       8       64        3      active sync set-B   /dev/sde

    # mount -a

###### 磁盘阵列＋热备份盘

RAID 10磁盘阵列中最多允许50%的磁盘设备发生故障，为了防止同一RAID 1磁盘阵列中的硬件设备全部损坏导致数据丢失，可以在准备一块容量足够大的磁盘，平时处于闲置状态，一旦发生故障后立即自动顶替上去；这个极端情况使用 mdadm -f 无法模拟出来，仅允许模拟50%的磁盘故障

    # mdadm /dev/md10 -f /dev/sd{b,c}
    mdadm: set /dev/sdb faulty in /dev/md10
    mdadm: set device faulty failed for /dev/sdc:  Device or resource busy

####### 添加热备份盘

再已经部署了RAID 10磁盘阵列的情况下，直接想阵列中加入一块磁盘即可

    # mdadm /dev/md10 -a /dev/sdf
    mdadm: added /dev/sdf
    # mdadm -D /dev/md10
    /dev/md10:
              Version : 1.2
        Creation Time : Fri Aug 24 15:41:15 2018
            Raid Level : raid10
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 4
        Total Devices : 5                           ＃总的设备数量变成５块
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 17:19:28 2018
                State : active 
        Active Devices : 4
      Working Devices : 5                         　＃可用设备变成５块
        Failed Devices : 0
        Spare Devices : 1                         　＃热备份盘１块

                Layout : near=2
            Chunk Size : 512K

    Consistency Policy : resync

                  Name : ops-saltmaster-1:10  (local to host ops-saltmaster-1)
                  UUID : 8d165aec:c19ebcfe:d4f87598:d6b74e83
                Events : 63

        Number   Major   Minor   RaidDevice State
          4       8       16        0      active sync set-A   /dev/sdb
          1       8       32        1      active sync set-B   /dev/sdc
          2       8       48        2      active sync set-A   /dev/sdd
          3       8       64        3      active sync set-B   /dev/sde

          5       8       80        -      spare   /dev/sdf      

####### 磁盘故障后热备份盘自动顶替

当/dev/sdd磁盘故障后，作为热备份盘的/dev/sdf立即自动顶替了，并自动开始数据修复

    # mdadm /dev/md10 -f /dev/sdd
    mdadm: set /dev/sdd faulty in /dev/md10
    # mdadm -D /dev/md10 
    /dev/md10:
              Version : 1.2
        Creation Time : Fri Aug 24 15:41:15 2018
            Raid Level : raid10
            Array Size : 41908224 (39.97 GiB 42.91 GB)
        Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
          Raid Devices : 4
        Total Devices : 5
          Persistence : Superblock is persistent

          Update Time : Fri Aug 24 17:26:45 2018
                State : active, degraded, recovering 　＃出现降级和修复状态
        Active Devices : 3
      Working Devices : 4
        Failed Devices : 1                            ＃出现一块故障设备
        Spare Devices : 1

                Layout : near=2
            Chunk Size : 512K

    Consistency Policy : resync

        Rebuild Status : 9% complete                  #数据修复的完成度

                  Name : ops-saltmaster-1:10  (local to host ops-saltmaster-1)
                  UUID : 8d165aec:c19ebcfe:d4f87598:d6b74e83
                Events : 66

        Number   Major   Minor   RaidDevice State
          4       8       16        0      active sync set-A   /dev/sdb
          1       8       32        1      active sync set-B   /dev/sdc
          5       8       80        2      spare rebuilding   /dev/sdf　　　#热备份盘已顶替上，并开始修复数据
          3       8       64        3      active sync set-B   /dev/sde

          2       8       48        -      faulty   /dev/sdd



<!--more-->




---

#### 参考文档

* 参考文档1

* 参考文档2

* 参考文档3
