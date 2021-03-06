---
title: 'Linux用户管理'
date: 2014-04-24 12:56:34
tags: [linux]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

### **一、用户管理：**

Linux系统用户分为三类：超级用户、普通用户和伪用户。其具体区别如下：

*   超级用户：具有管理系统的一切权限。UID为０。
*   普通用户：具有有限的权限。UID从500到6000。
*   伪用户：在/etc/passwd中有记录，但因记录中shell为空，所以不能登录系统。其作用是为了方便管理系统，满足相应的系统进程对文件属主的需要。

用户帐号的配置文件是/etc/passwd，可用sudo gedit /etc/passwd命令查看和修改。文件内容格式如下：
```
            root:x:0:0:root:/root:/bin/bash
            daemon:x:1:1:daemon:/usr/sbin:/bin/sh
            bin:x:2:2:bin:/bin:/bin/sh
            sys:x:3:3:sys:/dev:/bin/sh
            sync:x:4:65534:sync:/bin:/bin/sync
            games:x:5:60:games:/usr/games:/bin/sh
            man:x:6:12:man:/var/cache/man:/bin/sh
            lp:x:7:7:lp:/var/spool/lpd:/bin/sh
            mail:x:8:8:mail:/var/mail:/bin/sh
            news:x:9:9:news:/var/spool/news:/bin/sh
            uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
    

        ……
```

其含义为：

用户名：用户密码：用户id：组id:用户相关信息：用户家目录：用户shell

用户密码的配置文件为/etc/shadow，同样用sudo gedit /etc/shadow命令查看。
```
        root:!:14866:0:99999:7:::
        daemon:*:14728:0:99999:7:::
        bin:*:14728:0:99999:7:::
        sys:*:14728:0:99999:7:::
        sync:*:14728:0:99999:7:::
        ……
        hplip:*:14728:0:99999:7:::
        gdm:*:14728:0:99999:7:::
        kelvin:$6$CILudWuh$Dv6cGVZlLBKoCXUubKYeo3W4s9GmBegBJPgiIRzlRZ7O1uYO6mEzLtv
        rfFEozeSjHB56R7.Qt/rRgADFjyPda1:14866:0:99999:7:::
        sshd:*:14867:0:99999:7:::
```    

任何用户都可查看passwd文件，但只有root才能查看shadow文件。shadow文件内容的含义为：

用户名：加密口令（！表示不能登录）：密码最后修改时间：密码最大时间间隔：最小时间间隔：警告时间：不活动时间：失效时间

创建一个帐号有如下方法：

1.  在passwd中增加一条记录；创建用户家目录；设置用户家目录的配置文件；设置用户口令；
2.  useradd 命令或adduser命令；用userdel或deluser命令删除用户，注意加-r选项才能删除用户家目录和所有用户信息。

### **二、用户组管理：**

用户组分为：

*   私有组：当创建一个新的用户，没有指定该用户所属组时，系统则建立一个和该用户同名的私有组。
*   标准组：除开私有组以外的所有组。

用户组的配置文件为/etc/group，用sudo gedit /etc/group命令查看该文件，其内容形式如下：
```
        root:x:0:
        daemon:x:1:
        bin:x:2:
        sys:x:3:
        adm:x:4:kelvin
        tty:x:5:
        disk:x:6:
        lp:x:7:
        mail:x:8:
        news:x:9:
        uucp:x:10:
        man:x:12:
        proxy:x:13:
        ……
```    

其含义为：

组名：口令：组id：成员

添加或删除组的命令：groupadd、addgroup、delgroup、groupdel。具体用法和adduser类似。

添加用户到组de命令：gpasswd -a 用户名 组名。

将用户从组中删除：gpasswd -d 用户名 组名。

groups 用户名：查看用户的组状态。即用户属于哪些组。

finger 用户名：查看用户信息。