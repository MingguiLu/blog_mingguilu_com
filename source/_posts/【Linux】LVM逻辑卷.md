---
title: 【Linux】LVM逻辑卷
tags:
  - linux
  - LVM
  - 逻辑卷
categories: 点滴随笔
photos: /images/photos/linux.jpg
date: 2018-08-23 01:17:06
description:
---

#### LVM逻辑卷简介

![](/images/20180823/20180823_01.jpg)

LVM利用Linux内核的device-mapper来实现存储系统的虚拟化（系统分区独立于底层硬件），实现存储空间的抽象化并在上面建立虚拟分区（virtual partitions），可以更简便地扩大和缩小分区，可以增删分区时无需担心某个硬盘上没有足够的连续空间，实现对硬盘分区的动态调整

#### LVM常用部署命令

| 功能 | 物理卷管理 | 卷组管理 | 逻辑卷管理 |
| :---: | :---: | :---: | :---: |
| 扫描 | pvscan | vgscan | lvscan |
| 建立 | pvcreate | vgcreate | lvcreate |
| 显示概要 | pvs | vgs | lvs |
| 显示详情 | pvdisplay | vgdisplay | lvdisplay |
| 删除 | pvremove | vgremove | lvremove |
| 扩展 |  | vgextend | lvextend |
| 缩小 |  | vgreduce | lvreduce |

<!--more-->

#### 部署LVM逻辑卷

##### 查看磁盘信息

    # fdisk -l | grep sd
    磁盘 /dev/sda：21.5 GB, 21474836480 字节，41943040 个扇区
    /dev/sda1   *        2048     2099199     1048576   83  Linux
    /dev/sda2         2099200    41943039    19921920   8e  Linux LVM
    磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
    /dev/sdb1            2048    20973567    10485760   83  Linux
    /dev/sdb2        20973568    39847935     9437184   83  Linux
    磁盘 /dev/sdd：21.5 GB, 21474836480 字节，41943040 个扇区
    磁盘 /dev/sdc：21.5 GB, 21474836480 字节，41943040 个扇区

##### 添加物理卷

    # pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda2  centos lvm2 a--  <19.00g    0 
    # pvcreate /dev/sdb{1,2} /dev/sdc
      Physical volume "/dev/sdb1" successfully created.
      Physical volume "/dev/sdb2" successfully created.
      Physical volume "/dev/sdc" successfully created.
    # pvs
      PV         VG     Fmt  Attr PSize   PFree 
      /dev/sda2  centos lvm2 a--  <19.00g     0 
      /dev/sdb1         lvm2 ---   10.00g 10.00g
      /dev/sdb2         lvm2 ---    9.00g  9.00g
      /dev/sdc          lvm2 ---   20.00g 20.00g

##### 创建逻辑卷组

    # vgs
      VG     #PV #LV #SN Attr   VSize   VFree
      centos   1   2   0 wz--n- <19.00g    0 
    # vgcreate vg0 /dev/sdb{1,2} /dev/sdc
      Volume group "vg0" successfully created
    # vgs
      VG     #PV #LV #SN Attr   VSize   VFree  
      centos   1   2   0 wz--n- <19.00g      0 
      vg0      3   0   0 wz--n- <38.99g <38.99g

##### 切割逻辑卷

切割逻辑卷时有2中计量单位：

* -L 以容量为单位，例如 -L 5120M 或 -L 5G 都可以生成1个5G的逻辑卷
* -l 以基本单元的个数为单位，每个基本单元的大小默认为4MB，例如 -l 1280 可生成1个5G的逻辑卷
* -l 还可以以逻辑卷组容量的百分比来切割，例如 -l 30%free 可生成当前逻辑卷组30%容量大小的逻辑卷

-n 可设定逻辑卷名称

    # lvs
      LV   VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
      root centos -wi-ao---- <17.00g                                                    
      swap centos -wi-ao----   2.00g                                                    
    # lvcreate -n lv_data -L 5G vg0
      Logical volume "lv_data" created.
    # lvcreate -n lv_log -l 30%free vg0
      Logical volume "lv_log" created.
    # lvs
      LV      VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
      root    centos -wi-ao---- <17.00g                                                    
      swap    centos -wi-ao----   2.00g                                                    
      lv_data vg0    -wi-a-----   5.00g                                                    
      lv_log  vg0    -wi-a----- <10.20g 

##### 格式化逻辑卷并挂载使用

    # mkfs.ext4 /dev/vg0/lv_data 
    mke2fs 1.42.9 (28-Dec-2013)
    文件系统标签=
    OS type: Linux
    块大小=4096 (log=2)
    分块大小=4096 (log=2)
    Stride=0 blocks, Stripe width=0 blocks
    327680 inodes, 1310720 blocks
    65536 blocks (5.00%) reserved for the super user
    第一个数据块=0
    Maximum filesystem blocks=1342177280
    40 block groups
    32768 blocks per group, 32768 fragments per group
    8192 inodes per group
    Superblock backups stored on blocks: 
      32768, 98304, 163840, 229376, 294912, 819200, 884736

    Allocating group tables: 完成                            
    正在写入inode表: 完成                            
    Creating journal (32768 blocks): 完成
    Writing superblocks and filesystem accounting information: 完成 

    # mkfs.xfs /dev/vg0/lv_log
    meta-data=/dev/vg0/lv_log        isize=512    agcount=4, agsize=668160 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0, sparse=0
    data     =                       bsize=4096   blocks=2672640, imaxpct=25
            =                       sunit=0      swidth=0 blks
    naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
    log      =internal log           bsize=4096   blocks=2560, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0

    # mkfs.ext4 /dev/vg0/lv_data 

    # mount /dev/vg0/lv_data /data
    # mount /dev/vg0/lv_log /log
    # df -h
    文件系统                 容量  已用  可用 已用% 挂载点
    /dev/mapper/centos-root   17G 1010M   17G    6% /
    devtmpfs                 899M     0  899M    0% /dev
    tmpfs                    911M     0  911M    0% /dev/shm
    tmpfs                    911M  9.6M  902M    2% /run
    tmpfs                    911M     0  911M    0% /sys/fs/cgroup
    /dev/sda1               1014M  142M  873M   14% /boot
    tmpfs                    183M     0  183M    0% /run/user/0
    /dev/mapper/vg0-lv_data  4.8G   20M  4.6G    1% /data
    /dev/mapper/vg0-lv_log    11G   33M   11G    1% /log

#####  设置永久挂载

    # echo "/dev/vg0/lv_data /data ext4 defaults 0 0" >> /etc/fstab 
    # echo "/dev/vg0/lv_log /log xfs defaults 0 0" >> /etc/fstab


