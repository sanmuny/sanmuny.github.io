---
title: '发行版制作及Anaconda基础'
date: 2014-07-30 17:08:46
tags: []
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

Anaconda是用于Fedora, RHEL等Linux发行版的安装程序，可以实现通过LiveCD,PXE,NFS等方式安装Linux系统以及利用kickstart文件实现无人值守的无交互安装。

发行版制作(Fedoran系统环境)
------------------

1.  选择所需的软件包。

制作自己的发行版首先要确定你的发行版需要安装哪些软件。决定之后需要把这些软件相应的RPM包拷贝到某个目录，然后在这个目录下执行createrepo命令来创建RPM软件源。

1.  创建ks文件。

ks文件用于指定制作发行版时用到的软件源及软件包，具体语法可参考 [kickstart语法](http://fedoraproject.org/wiki/Anaconda/Kickstart)，下面是一个简单的例子：

编译及安装
-----

*   获取源码：git clone git://git.fedorahosted.org/git/anaconda.git
*   安装依赖包： sudo yum install libtool $(grep ^BuildRequires: anaconda.spec.in | awk '{print $2}')
*   安装、配置transifex：sudo yum install transifex-client；tx init /tmp
*   ./autogen.sh && ./configure && make po-pull && make

源码目录结构
------

1.  接口：pyanaconda/ui/
    
    *   gui/：图形界面接口实现代码。
    *   tui/：字符界面及命令行界面实现代码。
    *   __init__.py 及 common.py：定义了gui和tui通用的基类(base class)
    *   communication.py：负责UI中类的通信。
2.  自定义组件：widgets/
    
    *   data/：存放时区地图组件的图片。
    *   glade/及python/：让用户接口构建器知道组件的存在及实现python的自省。
    *   src/：实现各组件。
3.  分区：python-blivet包
    
4.  Bootloader： pyanaconda/bootloader.py
    
5.  各个步骤的配置：
    
    *   pyanaconda/desktop.py
    *   pyanaconda/keyboard.py
    *   pyanaconda/localization.py
    *   pyanaconda/network.py
    *   pyanaconda/ntp.py
    *   pyanaconda/timezone.py
    *   pyanaconda/users.py
6.  安装软件包：
    
    *   pyanaconda/packaging/
    *   scripts/anaconda-yum
7.  安装类： 不同的发行版可以定义不同的安装类。
    
    *   pyanaconda/installclass.py
    *   pyanaconda/installclasses/
    *   pyanaconda/product.py
8.  无人值守安装：pyanaconda/kickstart.py
    
9.  liveCD：
    
    *   data/icons/
    *   data/liveinst/
10.  错误处理：
    
    *   pyanaconda/errors.py
    *   pyanaconda/exception.py
11.  安装控制库
    
    *   pyanaconda/install.py：控制安装步骤。
    *   pyanaconda/progress.py：控制进度条。
    *   pyanaconda/queue.py：控制通信队列。
    *   pyanaconda/threads.py：多线程支持。
12.  库：提供一些工具如获得用户位置，安装日志等。
    
    *   pyanaconda/**init**.py
    *   pyanaconda/addons.py
    *   pyanaconda/anaconda_log.py
    *   pyanaconda/anaconda_optparse.py
    *   pyanaconda/constants.py
    *   pyanaconda/flags.py
    *   pyanaconda/geoloc.py
    *   pyanaconda/i18n.py
    *   pyanaconda/image.py
    *   pyanaconda/indexed_dict.py
    *   pyanaconda/isys/
    *   pyanaconda/iutil.py
    *   pyanaconda/nm.py
    *   pyanaconda/safe_dbus.py
    *   pyanaconda/simpleconfig.py
    *   pyanaconda/sitecustoimze.py
13.  主程序anaconda：由systemd在系统启动后调用，设置环境、VNC等。
    
14.  启动
    
    *   data/systemd/
    *   dracut/
15.  内存监控
    
    *   scripts/anaconda-cleanup：监控安装过程中的内存状态，并记录到/tmp/memory.dat文件中。
    *   scripts/instperf及scripts/instperf.p：利用memory.dat文件生成相应的图表。
16.  升级工具
    
    *   scripts/makebumpver
    *   scripts/makeupdates