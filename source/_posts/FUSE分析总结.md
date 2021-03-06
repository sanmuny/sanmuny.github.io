---
title: 'FUSE分析总结'
date: 2014-04-24 14:09:12
tags: [fuse]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

###**FUSE简介及原理**

FUSE（Filesystem in Userspace）是sourceforge上的一个开源项目，它可以为用户提供编写用户态文件系统的接口。使用FUSE，用户可以不必熟悉Kernel代码，使用标准C库、FUSE库以及GNU C库便可设计出自己需要的文件系统。

FUSE由三个部分组成：FUSE内核模块、FUSE库以及一些挂载工具。

FUSE内核模块实现了和VFS的对接，它看起来像一个普通的文件系统模块；另外，FUSE内核模块实现了一个可以被用户空间进程打开的设备，当VFS发来文件操作请求之后，它将该请求转化为特定格式，并通过设备传递给用户空间进程，用户空间进程在处理完请求后，将结果返回给FUSE内核模块，内核模块再将其还原为Linux kernel需要的格式，并返回给VFS。

FUSE库负责和内核空间的通信，它接收来自/dev/fuse的请求，并将其转化为一系列的函数调用，并将结果写回到/dev/fuse。

挂载工具用以实现对用户态文件系统的挂载。

当系统用户在输入ls /home/kelvin命令之后，最终会调用到Ext4文件系统内的相关函数来对文件进行处理，并将结果返回。

系统用户在该文件系统（/tmp/fuse为tfs的挂载点）内所执行的ls –l /tmp/fuse命令通过FUSE最终会调用到tfs里所写的钩子函数。这样，通过FUSE，可以在用户态设计并实现自己的文件系统，而不必了解kernel的代码编写规范。

###**FUSE代码编写规范**

FUSE给用户提供了fuse_operations结构体，用户可实现具体的钩子函数，然后将这些钩子函数挂载到该结构体。main()函数只需调用fuse_main()就可以了，其他的工作交给FUSE去做。

###**fuse_main()的处理流程**

fuse_main()被调用后，它调用fuse_mount()，创建新的进程fusermount，来检查FUSE内核模块是否加载，并返回文件描述符给fuse_main()。fuse_new()为文件系统分配数据空间。fuse_loop()从/dev/fuse 读取文件系统调用，调用fuse_operations结构中的处理函数，返回调用结果给/dev/fuse。

### **使用FUSE的注意事项**

*   FUSE的作用在于使用户能够绕开内核代码来编写文件系统，可文件系统如果要实现对具体的设备的操作的话必须要使用设备驱动提供的接口，而设备驱动位于内核空间，FUSE便无法将文件系统挂载到具体设备上去。所以，基于FUSE所写的文件系统通常是将文件当做虚拟的磁盘，并使用C所提供的文件操作接口；或者是映射一个目录到文件系统。
    
*   FUSE给各钩子函数传递的path参数的/指的是文件系统的/目录。