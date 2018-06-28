---
title: Account locked due to 13 failed logins
tags:
  - account locked 
  - ssh账户锁定
  - ssh账户解锁
categories: 运维之道
photos: 
date: 2018-06-27 10:25:38
description:
---

ssh登录时密码错误次数超限后账户锁定，解锁方法如下：

    pam_tally2 --user=ituser  #查看用户登录计数器
    pam_tally2 --user=ituser --reset #重置计数器，即解锁用户


极端情况下，已经无法登录到服务器上进行解锁，需要使用salt工具等远程解锁：

<!-- more -->

    [root@ops-saltmast-01 ~]# ssh ituser@172.30.32.50
    Account locked due to 13 failed logins
    Password:

    [root@ops-saltmast-01 ~]#
    [root@ops-saltmast-01 ~]# salt 'prd-nginx-03' cmd.run 'pam_tally2 --user=ituser'
    WARNING: yacc table file version is out of date
    prd-nginx-03:
        Login           Failures Latest failure     From
        ituser           13    06/28/18 10:01:42  172.30.33.183
    [root@ops-saltmast-01 ~]#
    [root@ops-saltmast-01 ~]# salt 'prd-nginx-03' cmd.run 'pam_tally2 --user=ituser --reset'
    WARNING: yacc table file version is out of date
    prd-nginx-03:
        Login           Failures Latest failure     From
        ituser           13    06/28/18 10:01:42  172.30.33.183
    [root@ops-saltmast-01 ~]#
    [root@ops-saltmast-01 ~]# salt 'prd-nginx-03' cmd.run 'pam_tally2 --user=ituser'
    WARNING: yacc table file version is out of date
    prd-nginx-03:
        Login           Failures Latest failure     From
        ituser            0

---

#### 参考文档

* 《Account locked due to 25 failed logins》：[https://www.cnblogs.com/cjsblogs/p/8794271.html](https://www.cnblogs.com/cjsblogs/p/8794271.html)

* 《使用Pam_Tally2锁定和解锁SSH失败的登录尝试》：[https://www.howtoing.com/use-pam_tally2-to-lock-and-unlock-ssh-failed-login-attempts/](https://www.howtoing.com/use-pam_tally2-to-lock-and-unlock-ssh-failed-login-attempts/)
