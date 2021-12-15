---
title: 'Linux权限管理'
date: 2014-04-24 12:43:36
tags: [linux]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

Linux权限管理是其一大特色，优秀的权限管理机制为Linux安全性提供了可靠的保障。

### **一、用户权限管理：**

root用户是系统的超级用户，是Linux系统的CEO,它具有最高的管理权限，所以一般不用该用户登录系统进行日常的操作与维护，root可将某些权限赋予其他用户来管理系统的某些资源。

su命令用来切换用户，用该命令切换用户时，用户身份和环境变量都发生改变。例如：

    kelvin@kelvin-laptop:~$ ls
    examples.desktop  workplace  公共的  模板  视频  图片  文档  下载  音乐  桌面
    kelvin@kelvin-laptop:~$ su ws
    密码： 
    ws@kelvin-laptop:/home/kelvin$ 
    

在ubuntu系统中，有些动作需要管理员的权限才能执行，可用sudo来提升权限。若在/etc/目录下建立nologin文件，则只允许root用户登录。相反，删除该文件则所有用户都可以登录系统。

### **二、文件权限管理：**

使用ls -l命令可以以长格式显示该目录下文件和子目录信息。如：

    kelvin@kelvin-laptop:~$ ls -l
    总用量 40
    -rw-r--r-- 1 kelvin kelvin  179 2010-09-14 09:39 examples.desktop
    lrwxrwxrwx 1 kelvin kelvin   37 2010-09-15 21:49 linux -> /home/kelvin/workplace/linux-2.6.35.4
    drwxr-xr-x 3 kelvin kelvin 4096 2010-09-15 17:13 workplace
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 公共的
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 模板
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 视频
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 图片
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 15:09 文档
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 14:57 下载
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 音乐
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-15 17:18 桌面
    

第一部分给出了文件或目录的相关权限信息：

    drwxr-xr-x：总共有十个位。
    

第1位给出了该项的属性，即是文件还是目录，或者是链接文件等。

    -：表示是文件，如上面的examples.desktop就是一个文件；
    d：表示是目录，如上面的workplace;
    l：表示是链接文件；
    

第2、3、4位给出了文件或目录所有者的权限，第5、6、7位给出了文件或目录所属用户组的权限，第8、9、10位给出了其他用户权限。

    r：为读权限。
    w：为写权限。
    x：为执行权限。目录的执行权限的意思是可以用cd命令进入该目录。
    

chmod命令：该命令用来修改文件或目录的访问权限。一般有两种使用方式：

第一种方式为：chmod a+r 文件或目录名

其中a可用u、g、o替换，+可用=、-替换，r可用w、x替换。

    a：表示修改所有用户的权限。包括u、g、r。
    u：表示只给文件或目录所有者修改权限。
    g：表示给文件所有组修改权限。
    o：表示给其他用户修改权限。
    +：表示增加某种权限。
    -：表示减去某种权限。
    =：表示赋予某种权限。
    

第二种方式为：

用数字表示权限：

r：用4表示 w：用2表示 x：用1表示。

则1：--x 2:-w- 3:-wx 4:r-- 5:r-x 6:rw- 7:rwx

于是可用“chmod 777 文件或目录名”命令来修改权限。三个7中第一个代表所有者权限，第二个代表所有组权限，第三个代表其他用户权限。当然，也可用类似于“chmod u+2 文件目录名”的方式来修改权限。

-R选项表示包括子目录的权限也改变。

chown命令：改变文件或目录所有者。-R选项同样表示包含子目录。

格式：chown 用户 文件或目录

chgrp:改变所属组，用法类似于chown。

chown group.user 文件或目录名，可同时改变所有者和所属组。

umask：用于设定文件或目录刚创建是的权限。目录为755+umask值=777，文件644+umask值=777。

粘着位t：对于权限值为777的目录可设置粘着位t，即：drwxrwxrwt。其含义为，任何用户的可在该目录中创建和修改自己的文件，也可以查看别人的文件，但不能删除或修改其他用户的文件。

s：对于可执行文件，若将该文件用户或组权限的x用s替换，则相应用户便具有了该执行文件拥有者或拥有组的身份。设置s位的作用可用如下的例子阐明：

保存用户密码的文件存放于/etc/shadow中，其权限为：

    -rw-r----- 1 root shadow 1067 2010-09-15 21:33 /etc/shadow
    

即：只有该文件的所有者root用户才能具有读写该文件的权利，那么为什么普通用户可以通过passwd命令修改自己的密码呢？我们看一下passwd命令这个可执行文件的权限：

    -rwsr-xr-x 1 root root 37140 2010-01-27 01:09 /usr/bin/passwd
    

该命令有s位，所以当普通用户执行该命令时，他便有了passwd所有者root的身份，自然可以修改属于 root用户的文件shadow。