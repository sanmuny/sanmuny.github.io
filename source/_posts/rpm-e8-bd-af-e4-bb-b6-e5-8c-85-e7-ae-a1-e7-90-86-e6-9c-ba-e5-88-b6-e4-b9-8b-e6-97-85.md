---
title: 'RPM软件包管理机制之旅'
date: 2013-11-04 17:30:25
tags: [RPM,spec,软件包管理]
published: true
hideInList: false
feature: 
isTop: false
---

    Linux下的man命令十分实用，可以查看Linux命令的手册。但这些手册只适用于忘记命令的选项时查询之用，如果用来学习Linux下类似于Git, RPM这样庞大的工具就有点吃力了，可谓事倍功半。我在学习Git的时候读过一篇文档——gittutorial，使用：

    $man gittutorial
    

命令可以调出该文档。这篇文档并不涵盖git的方方面面，只是介绍了Git管理项目的常规用法，非常适合初学者快速入门。所以本文试图仿照该文档的写法，并不会详细地介绍RPM包的方方面面，而是介绍RPM包最常用的一些功能，这些功能将会在我们对红帽系列发行版系统的日常管理中非常重要。

_软件包管理机制与RPM_

   开源软件可以用Tarball的方式来从源代码安装软件(1)，这样的安装方式对普通用户来说很麻烦。比如要升级软件，还需要先更新源代码，再重新编译、安装。Linux采用了RPM和DPKG等软件包管理机制来管理软件，直接给用户提供二进制软件包，并且将整个系统的软件包信息建立成数据库，以便于软件的升级、验证和卸载。RedHat、Fedora、SUSE、CentOS等发行版采用RPM包管理机制，而Debian、Ubuntu等发行版采用DPKG方式来管理软件包。

   所谓RPM软件包或者平时叫的RPM包指的是包含软件运行所需的二进制文件、文档、函数库等内容的RPM格式的文件，以rpm作为文件的后缀名。如：

    qemu-img-1.4.2-3.fc19.i686.rpm
    

qemu-img是包的名字；1.4.2是软件版本号；3是release号，指的是同一版本第3次构建的软件包(或称为打包)；fc19指的是Linux发行版为Fedora 19；i686是软件运行的平台架构，可以是i386、i686、x86_64、ppc64、s390x、noarch(与平台无关的软件包)等，RPM要求打包的环境要与安装软件包的环境(平台架构、发行版、发行版的版本等)一致或相当。

_RPM软件包的增删改查及验证_

_1\. 安装RPM软件包到系统_

   现在的系统很少用到直接使用RPM包来安装软件，而是采用YUM(后面介绍)来安装。使用RPM包来安装软件首先需要获得适合当前系统的RPM包，各个发行版通常会提供这些包的下载，如可以在i686平台、fedora19上安装的软件包可以在这里找到：

[http://mirrors.ustc.edu.cn/fedora/linux/releases/19/Fedora/i386/os/Packages/](http://mirrors.ustc.edu.cn/fedora/linux/releases/19/Fedora/i386/os/Packages/)

   安装软件包可以使用rpm命令，加上-i选项即可。例如，安装qemu-img软件包可以采用如下命令：

    #rpm -ivh qemu-img-1.4.2-3.fc19.i686.rpm
    

-i选项表示rpm命令将安装qemu-img软件包；-v选项可以显示更详细的安装信息；-h显示安装进度。采用rpm -i命令来安装软件包并不会去解决软件包之间的依赖问题。如果说软件包B依赖于软件包A，那么在安装B之前A必须已经在系统中安装。rpm -i如果遇到依赖关系的问题，只会提示需要依赖哪些包，而不会去自动地将这些包都安装上。qemu-img依赖于glib库，如果我们将系统上的glib包删掉，再执行rpm -i来安装qemu-img就会产生如下的提示信息：

    [root[@localhost](https://my.oschina.net/u/570656) 下载]# rpm -e glib2-2.36.3-3.fc19.i686 --nodeps 
    [root[@localhost](https://my.oschina.net/u/570656) 下载]# rpm -q glib2-2.36.3-3.fc19.i686
    未安装软件包 glib2-2.36.3-3.fc19.i686 
    [root[@localhost](https://my.oschina.net/u/570656) 下载]# rpm -ivh qemu-img-1.4.2-3.fc19.i686.rpm 
    错误：依赖检测失败：
    libglib-2.0.so.0 被 qemu-img-2:1.4.2-3.fc19.i686 需要
    libgthread-2.0.so.0 被 qemu-img-2:1.4.2-3.fc19.i686 需要
    

   如果要安装qemu-img岂不是要手动地将所有依赖的包安装上去才行？而且，qemu-img所依赖的包可能又会依赖另外的包。如此一来，安装RPM包岂不是非常复杂？聪明的Linux hacker们当然不会让Linux的世界乱套。YUM就是为了解决软件包之间的依赖性而生，我们稍后会对YUM做进一步介绍，现在先抛开包的依赖性不说，看看如何在红帽系列发行版上来删除RPM软件包。

_2\. 删除RPM软件包_

   删除RPM软件包的命令和安装命令同样简单，只需要在rpm命令之上使用-e选项即可，同样值得一提的是删除软件包也需要注意软件包之间的依赖性，比如说，如果软件包A依赖于软件包B，那么应该先卸载软件包A，再卸载B，否则会导致软件A不可用。再用qemu-img的软件包为例，qemu-img会被libvirt-deamon所依赖，所以在删除qemu-img的时候会提示错误：

    [root[@localhost](https://my.oschina.net/u/570656) 下载]# rpm -e qemu-img 
    错误：依赖检测失败：
    /usr/bin/qemu-img 被 (已安裝) libvirt-daemon-1.0.5.6-3.fc19.i686 需要
    

_3\. 升级RPM软件包_

   如果系统上已经安装了glib2-2.36.3-2.fc19.i686这个包，现在发现在fedora的官网上提供了更新包glib2-2.36.3-3.fc19.i686，那该如何升级呢？使用rpm -Uvh或者rpm -Fvh即可。-U和-F的区别在于，-U允许系统未安装将要升级的包，在升级的时候执行安装动作，而-F则不能升级未安装的包。我们使用下面的命令可以将glib2升级：

    [root[@localhost](https://my.oschina.net/u/570656) 下载]# rpm -q glib2-2.36.3-2.fc19.i686
    glib2-2.36.3-2.fc19.i686
    [root@localhost 下载]# rpm -Uvh http://mirrors.ustc.edu.cn/fedora/linux/updates/19/i386/glib2-2.36.3-3.fc19.i686.rpm
    [root@localhost 下载]# rpm --rebuilddb 
    [root@localhost 下载]# rpm -q glib2-2.36.3-2.fc19.i686
    未安装软件包 glib2-2.36.3-2.fc19.i686 
    [root@localhost 下载]# rpm -q glib2-2.36.3-3.fc19.i686
    glib2-2.36.3-3.fc19.i686
    

_4\. 查看RPM软件包信息_

   有时候，我们需要查询软件包的信息，rpm -q选项提供了查询RPM包信息的一些功能。

例如：

查询软件包qemu-img-1.4.2-3.fc19.i686.rpm是否安装到系统：

    [kelvin@localhost 下载]$ rpm -qp qemu-img-1.4.2-3.fc19.i686.rpm 
    qemu-img-1.4.2-3.fc19.i686
    

-qp后面指定具体的包名，用于查询某具体的RPM包的信息，而不是已经安装到系统的RPM包的信息，如果想要查询已经安装到系统的包的信息，可以去掉-p选项。

在安装一个RPM包之前，我们通常想知道它安装了哪些文件：

    [kelvin@localhost 下载]$ rpm -qpl qemu-img-1.4.2-3.fc19.i686.rpm 
    /usr/bin/qemu-img
    /usr/bin/qemu-io
    /usr/bin/qemu-nbd
    /usr/share/man/man1/qemu-img.1.gz
    /usr/share/man/man8/qemu-nbd.8.gz
    

-q选项也可以被用来查询软件包的依赖关系：

    [kelvin@localhost 下载]$ rpm -qpR qemu-img-1.4.2-3.fc19.i686.rpm 
    libaio.so.1
    libaio.so.1(LIBAIO_0.1)
    libaio.so.1(LIBAIO_0.4)
    libc.so.6
    ......
    rtld(GNU_HASH)
    rpmlib(PayloadIsXz) <= 5.2-1
    

如果想知道一个文件是属于哪个文件包的，可以通过下面的命令来实现：

    [kelvin@localhost 下载]$ rpm -qf /usr/bin/qemu-img
    qemu-img-1.4.2-12.fc19.i686
    

_5\. 验证软件包中的文件是否被修改过_

   RPM机制提供了一种非常实用的功能，可以让我们查看到系统中哪些软件包的文件被修改过，从而可以看出是否有病毒或者是恶意软件。我们可以使用rpm -Va来查看系统中所有被更该过的文件，如果有二进制文件被改动过，我们应该特别留意这些文件。下面的例子演示了被修改过的/usr/bin/qemu-img文件通过强制重新安装来还原的过程：

    [root@localhost 下载]# rpm -qf /usr/bin/qemu-img 
    qemu-img-1.4.2-12.fc19.i686
    [root@localhost 下载]# echo "abc" >> /usr/bin/qemu-img 
    [root@localhost 下载]# rpm -Va | grep .*qemu.*
    S.5....T.    /usr/bin/qemu-img
    [root@localhost 下载]# rpm -ivh qemu-img-1.4.2-3.fc19.i686.rpm --force
    准备中...                          ################################# [100%]
    正在升级/安装...
       1:qemu-img-2:1.4.2-3.fc19          ################################# [100%]
    [root@localhost 下载]# rpm -Va | grep .*qemu.*
    .........    /usr/bin/qemu-img (已被替换)
    .........    /usr/bin/qemu-io (已被替换)
    .........    /usr/bin/qemu-nbd (已被替换)
    .........  d /usr/share/man/man1/qemu-img.1.gz (已被替换)
    .........  d /usr/share/man/man8/qemu-nbd.8.gz (已被替换)
    

_使用YUM来解决RPM包的依赖问题_

   前面提到过，使用rpm命令来安装和卸载软件，处理RPM包之间的依赖关系非常复杂。YUM(Yellow dog Updater, Modified, YUM)很好地解决了软件包之间依赖关系地问题，在安装、升级、卸载RPM包的时候可以自动地将依赖包也一并安装或卸载。一般来说，在系统装好之后就可以直接使用yum命令了，不用进行额外地配置。如果想了解如何去创建和配置YUM，请参考YUM的文档(2)。

使用YUM来安装qemu-img：

    [root@localhost ~]# yum install qemu-img
    已加载插件：fastestmirror, ibm-repository, langpacks, refresh-packagekit
    Loading mirror speeds from cached hostfile
     * fedora: mirrors.yun-idc.com
     * openclient: ocfedora.hursley.ibm.com
     * rpmfusion-free: mirror.bjtu.edu.cn
     * rpmfusion-free-updates: mirror.bjtu.edu.cn
     * rpmfusion-nonfree: mirror.bjtu.edu.cn
     * rpmfusion-nonfree-updates: mirror.bjtu.edu.cn
     * updates: mirrors.yun-idc.com
    正在解决依赖关系
    --> 正在检查事务
    ---> 软件包 qemu-img.i686.2.1.4.2-12.fc19 将被 安装
    --> 解决依赖关系完成
    
    依赖关系解决
        
        ========================================================================================================================================
         Package                        架构                       版本                                     源                             大小
        ========================================================================================================================================
        正在安装:
         qemu-img                       i686                       2:1.4.2-12.fc19                          updates                       454 k
        
        事务概要
        ========================================================================================================================================
        安装  1 软件包
        
        总下载量：454 k
        安装大小：2.0 M
        Is this ok [y/d/N]: y
        Downloading packages:
        Not downloading Presto metadata for updates
        qemu-img-1.4.2-12.fc19.i686.rpm                                                                                  | 454 kB  00:00:00     
        Running transaction check
        Running transaction test
        Transaction test succeeded
        Running transaction
          正在安装    : 2:qemu-img-1.4.2-12.fc19.i686                                                                                       1/1 
          验证中      : 2:qemu-img-1.4.2-12.fc19.i686                                                                                       1/1 
        
        已安装:
          qemu-img.i686 2:1.4.2-12.fc19                                                                                                         
        
        完毕！
    

可以看到，在安装qemu-img的时候，yum install 后面只需要接包名即可，无需完整的RPM文件名，而且YUM也会自动的解决安装包的依赖关系。

使用YUM来删除qemu-img：

    [root@localhost ~]# yum remove qemu-img
    已加载插件：fastestmirror, ibm-repository, langpacks, refresh-packagekit
    正在解决依赖关系
    --> 正在检查事务
    ---> 软件包 qemu-img.i686.2.1.4.2-12.fc19 将被 删除
    --> 正在处理依赖关系 /usr/bin/qemu-img，它被软件包 libvirt-daemon-1.0.5.6-3.fc19.i686 需要
    --> 正在使用新的信息重新解决依赖关系
    --> 正在检查事务
    ---> 软件包 libvirt-daemon.i686.0.1.0.5.6-3.fc19 将被 删除
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-interface-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-nodedev-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-uml-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-nwfilter-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-secret-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-network-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-lxc-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-config-nwfilter-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-storage-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-libxl-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-config-network-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-qemu-1.0.5.6-3.fc19.i686 需要
    --> 正在处理依赖关系 libvirt-daemon = 1.0.5.6-3.fc19，它被软件包 libvirt-daemon-driver-xen-1.0.5.6-3.fc19.i686 需要
    --> 正在检查事务
    ---> 软件包 libvirt.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-config-network.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-config-nwfilter.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-driver-interface.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-driver-libxl.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-driver-lxc.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-driver-network.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-driver-nodedev.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-driver-nwfilter.i686.0.1.0.5.6-3.fc19 将被 删除
    ---> 软件包 libvirt-daemon-driver-qemu.i686.0.1.0.5.6-3.fc19 将被 删除
    ......
    0:1.0.5.6-3.fc19              
          libvirt-daemon-driver-interface.i686 0:1.0.5.6-3.fc19               libvirt-daemon-driver-libxl.i686 0:1.0.5.6-3.fc19                 
          libvirt-daemon-driver-lxc.i686 0:1.0.5.6-3.fc19                     libvirt-daemon-driver-network.i686 0:1.0.5.6-3.fc19               
          libvirt-daemon-driver-nodedev.i686 0:1.0.5.6-3.fc19                 libvirt-daemon-driver-nwfilter.i686 0:1.0.5.6-3.fc19              
          libvirt-daemon-driver-qemu.i686 0:1.0.5.6-3.fc19                    libvirt-daemon-driver-secret.i686 0:1.0.5.6-3.fc19                
          libvirt-daemon-driver-storage.i686 0:1.0.5.6-3.fc19                 libvirt-daemon-driver-uml.i686 0:1.0.5.6-3.fc19                   
          libvirt-daemon-driver-xen.i686 0:1.0.5.6-3.fc19                    
    
    完毕！
    

YUM在删除qemu-img的同时，也会将依赖于qemu-img的libvirt删除掉，这样便不会引起因依赖包的缺失而导致的软件无法使用的问题。

使用YUM来升级qemu-img：

    [root@localhost ~]# yum update qemu-img
    已加载插件：fastestmirror, ibm-repository, langpacks, refresh-packagekit
    Loading mirror speeds from cached hostfile
     * fedora: mirrors.yun-idc.com
     * openclient: ocfedora.hursley.ibm.com
     * rpmfusion-free: mirror.bjtu.edu.cn
     * rpmfusion-free-updates: mirror.bjtu.edu.cn
     * rpmfusion-nonfree: mirror.bjtu.edu.cn
     * rpmfusion-nonfree-updates: mirror.bjtu.edu.cn
     * updates: mirrors.yun-idc.com
    No packages marked for update
    

qemu-img已经是最新版本。

使用YUM来查看qemu-img的信息：

    [root@localhost ~]# yum info qemu-img
    已加载插件：fastestmirror, ibm-repository, langpacks, refresh-packagekit
    Loading mirror speeds from cached hostfile
     * fedora: mirrors.yun-idc.com
     * openclient: ocfedora.hursley.ibm.com
     * rpmfusion-free: mirror.bjtu.edu.cn
     * rpmfusion-free-updates: mirror.bjtu.edu.cn
     * rpmfusion-nonfree: mirror.bjtu.edu.cn
     * rpmfusion-nonfree-updates: mirror.bjtu.edu.cn
     * updates: mirrors.yun-idc.com
    可安装的软件包
    名称    ：qemu-img
    架构    ：i686
    时期       ：2
    版本    ：1.4.2
    发布    ：12.fc19
    大小    ：454 k
    源    ：updates/19/i386
    简介    ： QEMU command line tool for manipulating disk images
    网址    ：http://www.qemu.org/
    协议    ： GPLv2+ and LGPLv2+ and BSD
    描述    ： This package provides a command line tool for manipulating disk images
    

YUM虽然好用，但毕竟是建立在RPM机制之上的，所以rpm命令也至关重要。

_制作自己的rpm包_

   既然使用RPM包管理机制能够很便捷地安装、升级以及卸载软件，那么该如何把自己的软件制作成RPM包呢？其实步骤很简单，只需要编辑自己的spec文件，然后使用rpmbuild命令来打包即可。spec文件告诉了rpmbuild究竟要制作什么样的软件包(包名，版本，作者等)，从哪获源代码，如何编译等。rpmbuild根据spec来制作满足需求的RPM包。所以，对于RPM打包来说，编写spec文件非常重要。如果想要很全面地学习spec文件的语法，可以阅读参考文献(3)和(4)，本文只是以制作开源项目HLFS(5)的RPM包为例，来说明如何打包。读者并不需要深入的了解HLFS，只需要知道HLFS是用c语言所写，依赖于glib、log4c等函数库，编译用到cmake即可。

_1.获取HLFS源代码_

   在安装rpmbuild之后，通常会在家目录下产生rpmbuild目录：

    [kelvin@localhost ~]$ ls -r ~/rpmbuild
    SRPMS  SPECS  SOURCES  RPMS  BUILD
    

   这几个子目录中，SPECS目录用来放置spec文件，SOURCES目录用来放置软件的源代码，RPMS放置打包生成的RPM包，SRPMS放置生成的SRPM包(包的内容是源代码和spec文件)，BUILD用来存放rpmbuild打包过程中临时用到的数据。

   在打包之前，需要把源代码先放到SOURCES目录下：

    [kelvin@localhost ~]$ cd ~/rpmbuild/SOURCES/
    [kelvin@localhost SOURCES]$ wget http://cloudxy.googlecode.com/files/hlfs.tar.gz
    

   源码拷贝之后需要在系统上安装编译HLFS所需要的工具cmake, glib等：

    [kelvin@localhost ~]$ sudo yum install cmake glib2-devel log4c-devel snappy-devel java-1.8.0-openjdk-devel hadoop-0.20-libhdfs 
    

_2.编辑hlfs.spec_

   在SPECS目录下创建hlfs.spec并写入以下内容：

    %define version 1.0
    
    Name: 	hlfs
    Version: %{version}
    Release:	1
    Summary: 	A distribuited and log-structured file system based on HDFS.
    
    Group: 	Development/Libraries	
    License: GPLv2	
    URL: 	https://code.google.com/p/cloudxy		
    Source0: 	hlfs-%{version}.tar.gz	
    
    Buildroot: 	/tmp/hlfs
    Requires: glib2 >= 2.0
    Requires: log4c >= 1.2.3
    Requires: snappy >= 1.1.0
    Requires: java-1.8.0-openjdk-devel >= 1.8.0.0
    Requires: hadoop-0.20-libhdfs >= 0.20.2
    BuildRequires: cmake
    BuildRequires: glib2-devel >= 2.0
    BuildRequires: log4c-devel >= 1.2.3
    BuildRequires: snappy-devel >= 1.1.0
    BuildRequires: java-1.8.0-openjdk-devel >= 1.8.0.0
    BuildRequires: hadoop-0.20-libhdfs >= 0.20.2
    
    %description
    
    HLFS(actually, Block level Storage System seems more proper than File System) 
    is a distributed VM image storage system for ECMS,which provides highly availab-
    le block level storage volumes that can be attached to XEN virtual machines by 
    its tapdisk driver.Similar project related to KVM is sheepdog，but they are in 
    different architectures.
    
    Compared with sheepdog, HLFS has greater scalability and reliability than sheep-
    dog by now,as we are on the shoulder of hadoop distribute file system (HDFS).Me-
    anwhile,HLFS also supports advanced volume management features such as snapshot(
    HLFS can also support snapshot tree)、cloning、thin provisioning and cache.
    
    The main idea of HLFS is:
    
    Take advantage of Log-structured File System's ideology to build an on-line ima-
    ge storage system on HDFS which can guarantee the reliability and scalability 
    for our storage system
    The ideology of LFS makes our storage system support random access to online im-
    ages.The ideology of LFS also makes our storage system more efficient and easily
    take snapshot.
    
    %prep
    %setup -q
    
    %build
    cd ./build
    cmake ../src
    make
    
    %install
    cd build
    make install DESTDIR=%{buildroot}
    
    
    %files
    %dir /usr/local/hlfs/bin
    %dir /usr/local/hlfs/include
    %dir /usr/local/hlfs/lib
    %dir /usr/local/hlfs
    /usr/local/hlfs/bin/clone.hlfs
    /usr/local/hlfs/bin/log4crc
    /usr/local/hlfs/bin/mkfs.hlfs
    /usr/local/hlfs/bin/nbd_ops
    /usr/local/hlfs/bin/rmfs.hlfs
    /usr/local/hlfs/bin/segcalc.hlfs
    /usr/local/hlfs/bin/segclean.hlfs
    /usr/local/hlfs/bin/snapshot.hlfs
    /usr/local/hlfs/bin/stat.hlfs
    /usr/local/hlfs/bin/tapdisk_ops
    /usr/local/hlfs/include/address.h
    /usr/local/hlfs/include/api/hlfs.h
    /usr/local/hlfs/include/base_ops.h
    /usr/local/hlfs/include/bs_hdfs.h
    /usr/local/hlfs/include/bs_local.h
    /usr/local/hlfs/include/cache.h
    /usr/local/hlfs/include/cache_helper.h
    /usr/local/hlfs/include/clone.h
    /usr/local/hlfs/include/cmd_define.h
    /usr/local/hlfs/include/comm_define.h
    /usr/local/hlfs/include/ctrl_region.h
    /usr/local/hlfs/include/hlfs_ctrl.h
    /usr/local/hlfs/include/hlfs_log.h
    /usr/local/hlfs/include/icache.h
    /usr/local/hlfs/include/logger.h
    /usr/local/hlfs/include/misc.h
    /usr/local/hlfs/include/seg_clean.h
    /usr/local/hlfs/include/seg_clean_helper.h
    /usr/local/hlfs/include/snapshot.h
    /usr/local/hlfs/include/snapshot_helper.h
    /usr/local/hlfs/include/storage.h
    /usr/local/hlfs/include/storage_helper.h
    /usr/local/hlfs/lib/libhlfs.so
    
    %clean
    rm -rf $RPM_BUILD_ROOT
    
    %changelog
    * Sat Aug 10 2013 Wang Sen<wangsen@linux.vnet.ibm.com> 1.0-1
    - First build.
    

   spec文件可以使用%define来定义宏，并且用%{name}来展开宏。文件开头将HLFS的版本号用宏来定义以方便以后的升级。从结构上来看，hlfs.spec大致分为以下几个部分：

    %description及包信息
    %prep
    %build
    %install
    %files
    %clean
    %changelog
    

   第一部分给出了HLFS软件包的一些基本信息，比如说包名、版本号、release号、项目的URL、源代码等信息。一般来说，对于同一版本的代码，每打包一次release号加1。Group指定了该软件的类型，rpm所有可用的类型可以在/usr/share/doc/rpm-(rpm版本)/GROUPS文件中查到。summary和description给出了软件包的介绍，这些内容可以通过rpm -qi或者yum info来查看到。Requires指定了HLFS需要依赖的包及其版本号，BuildRequires指出了打包需要的软件包及其版本号。

   %prep部分的主要作用是将源代码解压缩解包以提供给编译过程，一般是通过%setup宏来实现。当然也可以不用这个宏，自己手动的实现。rpmbuild常用的宏是在以下几个文件中定义的，需要时可以查看：

    /usr/lib/rpm/macros
    /usr/lib/rpm/redhat/macros
    /etc/rpm/macros
    ~/.rpmmacros
    

   %build部分是将上面准备好的源代码通过shell命令来编译成二进制可执行文件。

   %install将编译好的二进制文件安装到rpmbuild所用的临时表示最终安装根目录的buildroot里。

   %files指定HLFS的RPM包所要包含的文件和目录，可以通过rpm -qpl来查看到这些文件列表。

   %clean部分用来删除打包过程中产生的临时文件。    %changelog用来记录spec文件的改动，在vim的命令模式下可以输入\\c来快速地插入作者信息及时间戳。

_3\. 使用rpmbuild打包_

   编写完spec文件之后就可以使用rpmbuild命令来打包了。选项-bb用来产生二进制包；-bs用来产生SRPM包；-ba生成两种类型地包。我们使用rpmbuild -ba来生成二进制包和源码包：

    [kelvin@localhost SPECS]$ rpmbuild -ba hlfs.spec 
    

   结果生成地二进制包会放到RPMS/${arch}目录下，源码包会放到SRPMS目录下。打包完成后可以通过rpm命令来检验所产生地RPM包：

    [kelvin@localhost ~]$ cd ~/rpmbuild/RPMS/i386/
    [kelvin@localhost i386]$ ls
    hlfs-1.0-1.i386.rpm
    [kelvin@localhost i386]$ rpm -qpl hlfs-1.0-1.i386.rpm 
    /usr/local/hlfs
    /usr/local/hlfs/bin
    /usr/local/hlfs/bin/clone.hlfs
    /usr/local/hlfs/bin/log4crc
    /usr/local/hlfs/bin/mkfs.hlfs
    /usr/local/hlfs/bin/nbd_ops
    /usr/local/hlfs/bin/rmfs.hlfs
    /usr/local/hlfs/bin/segcalc.hlfs
    /usr/local/hlfs/bin/segclean.hlfs
    /usr/local/hlfs/bin/snapshot.hlfs
    /usr/local/hlfs/bin/stat.hlfs
    /usr/local/hlfs/bin/tapdisk_ops
    /usr/local/hlfs/include
    /usr/local/hlfs/include/address.h
    /usr/local/hlfs/include/api/hlfs.h
    /usr/local/hlfs/include/base_ops.h
    /usr/local/hlfs/include/bs_hdfs.h
    /usr/local/hlfs/include/bs_local.h
    /usr/local/hlfs/include/cache.h
    /usr/local/hlfs/include/cache_helper.h
    /usr/local/hlfs/include/clone.h
    /usr/local/hlfs/include/cmd_define.h
    /usr/local/hlfs/include/comm_define.h
    /usr/local/hlfs/include/ctrl_region.h
    /usr/local/hlfs/include/hlfs_ctrl.h
    /usr/local/hlfs/include/hlfs_log.h
    /usr/local/hlfs/include/icache.h
    /usr/local/hlfs/include/logger.h
    /usr/local/hlfs/include/misc.h
    /usr/local/hlfs/include/seg_clean.h
    /usr/local/hlfs/include/seg_clean_helper.h
    /usr/local/hlfs/include/snapshot.h
    /usr/local/hlfs/include/snapshot_helper.h
    /usr/local/hlfs/include/storage.h
    /usr/local/hlfs/include/storage_helper.h
    /usr/local/hlfs/lib
    /usr/local/hlfs/lib/libhlfs.so
    [kelvin@localhost i386]$ rpm -qpi hlfs-1.0-1.i386.rpm 
    Name        : hlfs
    Version     : 1.0
    Release     : 1
    Architecture: i386
    Install Date: (not installed)
    Group       : Development/Libraries
    Size        : 851341
    License     : GPLv2
    Signature   : (none)
    Source RPM  : hlfs-1.0-1.src.rpm
    Build Date  : 2013年11月04日 星期一 17时26分34秒
    Build Host  : localhost.localdomain
    Relocations : (not relocatable)
    URL         : https://code.google.com/p/cloudxy
    Summary     : A distribuited and log-structured file system based on HDFS.
    Description :
    
    HLFS(actually, Block level Storage System seems more proper than File System)
    is a distributed VM image storage system for ECMS,which provides highly availab-
    le block level storage volumes that can be attached to XEN virtual machines by
    its tapdisk driver.Similar project related to KVM is sheepdog，but they are in
    different architectures.
    
    Compared with sheepdog, HLFS has greater scalability and reliability than sheep-
    dog by now,as we are on the shoulder of hadoop distribute file system (HDFS).Me-
    anwhile,HLFS also supports advanced volume management features such as snapshot(
    HLFS can also support snapshot tree)、cloning、thin provisioning and cache.
    
    The main idea of HLFS is:
    
    Take advantage of Log-structured File System's ideology to build an on-line ima-
    ge storage system on HDFS which can guarantee the reliability and scalability
    for our storage system
    The ideology of LFS makes our storage system support random access to online im-
    ages.The ideology of LFS also makes our storage system more efficient and easily
    take snapshot.
    [kelvin@localhost i386]$ rpm -qpR hlfs-1.0-1.i386.rpm 
    glib2 >= 2.0
    hadoop-0.20-libhdfs >= 0.20.2
    java-1.8.0-openjdk-devel >= 1.8.0.0
    libc.so.6
    libc.so.6(GLIBC_2.0)
    libc.so.6(GLIBC_2.1)
    libc.so.6(GLIBC_2.1.3)
    libglib-2.0.so.0
    libgthread-2.0.so.0
    libhdfs.so.0
    libjvm.so
    liblog4c.so.3
    librt.so.1
    libsnappy.so.1
    log4c >= 1.2.3
    rpmlib(CompressedFileNames) <= 3.0.4-1
    rpmlib(PayloadFilesHavePrefix) <= 4.0-1
    rtld(GNU_HASH)
    snappy >= 1.1.0
    

_小结_

   本文通过实例介绍了RPM包的使用以及打包方法，适合初学者快速地掌握RPM软件包机制，如果需要更加详细地了解RPM软件包机制，请阅读参考文献(4)和(5)。

_参考文献_

1\. 鸟哥的Linux私房菜——基础学习篇，第三版，22章。  
2\. YUM文档：[http://yum.baseurl.org/wiki/Guides](http://yum.baseurl.org/wiki/Guides)  
3\. Fedora RPM Guide：[http://docs.fedoraproject.org/en-US/Fedora\_Draft\_Documentation/0.1/html/RPM_Guide/index.html](http://docs.fedoraproject.org/en-US/Fedora_Draft_Documentation/0.1/html/RPM_Guide/index.html)  
4\. Maximum RPM：[http://ishare.iask.sina.com.cn/f/37848528.html](http://ishare.iask.sina.com.cn/f/37848528.html)  
5\. HLFS Project Home：[http://code.google.com/p/cloudxy/](http://code.google.com/p/cloudxy/)