---
title: 'QEMU3 - 使用ceph来存储QEMU镜像'
date: 2016-09-30 14:56:31
tags: [ceph,qemu,virtualization]
published: true
hideInList: false
feature: 
isTop: false
categories: 云计算
---

ceph简介
======

Ceph是一个PB级别的分布式软件定义存储系统，为用户提供了块存储、对象存储以及符合POSIX标准的文件系统接口。目前，Ceph已经成为Openstack最受欢迎的后端存储系统。下图为ceph的架构图。

RADOS本身是一个对象存储系统，实现了ceph的核心功能。Librados是ceph提供给各种编程语言的接口。RADOSGW,RBD,CEPH FS分别为用户提供了对象存储、块存储及文件系统的功能。Ceph集群及客户端的安装配置请参考[Ceph官方文档](http://docs.ceph.com/docs/master/install/)。

使用Ceph来存储QEMU镜像
===============

QEMU会假定ceph配置文件存放在默认位置/etc/ceph/$cluster.conf，也会使用client.admin作为默认的ceph用户。如果要指定其他的配置文件或者用户，可以在ceph RBD的选项中添加conf=/home/ceph.conf或者id=admin选项。qemu-img使用ceph块存储RBD时，需要使用下面的格式：

    qemu-img {command} [options] rbd:{pool-name}/{image-name}[@snapshot-name][:option1=value1][:option2=value2...] 

例如：

    qemu-img {command} [options] rbd:glance-pool/maipo:id=glance:conf=/etc/ceph/ceph.conf 

创建一个镜像
------

可以使用qemu-img命令在ceph集群中创建一个虚拟机镜像。需要指定rbd, pool,以及镜像名。

    qemu-img create -f raw rbd:{pool-name}/{image-name} {size} 

例如：

    [root@ltczhp20 ~]# qemu-img create -f raw rbd:rbd/vmdisk1 4G
    
    Formatting 'rbd:rbd/vmdisk1', fmt=raw size=4294967296
    [root@ltczhp20 ~]# rbd ls
    vmdisk1

qemu-img通常会指定RBD存储的镜像格式是RAW，这样可以减少其他格式带来的性能开销，也会防止虚拟机热迁移时缓存带来的问题。

**调整镜像的大小**
-----------

要调整镜像大小，必须指定rbd,pool name,以及镜像名。

    qemu-img resize rbd:{pool-name}/{image-name} {size} 

例如：
```
    [root@ltczhp20 ~]# qemu-img resize -f raw rbd:rbd/vmdisk1 2G
    
    Image resized.
    [root@ltczhp20 ~]# rbd ls
    vmdisk1
    [root@ltczhp20 ~]# rbd info vmdisk1
    rbd image 'vmdisk1':
        size 2048 MB in 512 objects
        order 22 (4096 kB objects)
        block_name_prefix: rbd_data.fa802ae8944a
        format: 2
        features: layering, exclusive-lock, object-map, fast-diff, deep-flatten
        flags:
```
如果不指定镜像格式(-f raw)，qemu会给出警告信息：
```
    [root@ltczhp20 ~]# qemu-img resize rbd:rbd/vmdisk1 4G
    WARNING: Image format was not specified for 'rbd:rbd/vmdisk1' and probing guessed raw.
             Automatically detecting the format is dangerous for raw images, write operations on block 0 will be restricted.
             Specify the 'raw' format explicitly to remove the restrictions.
    Image resized.
```
**获取镜像信息**
----------

获取镜像信息同样需要指定rbd,pool name以及镜像名：

    qemu-img info rbd:{pool-name}/{image-name}

例如：

    [root@ltczhp20 ~]# qemu-img info rbd:rbd/vmdisk1
    
    image: rbd:rbd/vmdisk1
    file format: raw
    virtual size: 4.0G (4294967296 bytes)
    disk size: unavailable
    cluster_size: 4194304

**使用qemu命令运行虚拟机**
-----------------

从QEMU0.15后，虚拟机使用ceph块设备就不需要使用rbd map命令将RBD镜像映射到本地了，QEMU可以通过librados直接访问一个虚拟块设备。这样避免了额外的上下文切换，也充分利用了RBD的缓存功能。

在运行虚拟机之前，我们可以把一个已经存在的虚拟机镜像转化为ceph RBD存储，然后直接从RBD启动虚拟机。

    qemu-img convert -c -f fmt -O out\_fmt -o options  fname out\_fname 

例如：

    [root@ltczhp20 ~]# qemu-img convert -f qcow2 -O raw /srv/fedora24/fedora24.qcow2 rbd:rbd/fedora

然后使用qemu命令运行虚拟机。

    [root@ltczhp20 ~]# qemu-system-s390x -nographic -enable-kvm -m 4G -drive format=raw,file=rbd:rbd/fedora

RBD缓存会极大的提高虚拟机的性能。QEMU1.2之后，cache选项可以直接控制librbd：

    [root@ltczhp20 ~]# qemu-system-s390x -nographic -enable-kvm -m 4G -drive format=raw,file=rbd:rbd/fedora,cache=writeback

在QEMU1.2之前，如果要使用RBD缓存，需要额外添加rbd_cache=true选项：

    [root@ltczhp20 ~]# qemu-system-s390x -nographic -enable-kvm -m 4G -drive format=raw,file=rbd:rbd/fedora,cache=writeback,rbd_cache=true

如果指定了rbd_cache=true，一定要指定cache=writeback，否则QEMU不会给librbd发送flush请求，RBD之上的文件系统可能会被破坏。

**使用ceph RBD的快照功能**
-------------------

创建一个镜像快照sp0：

    [root@ltczhp20 ~]# qemu-img snapshot -l rbd:rbd/fedora
    [root@ltczhp20 ~]# qemu-img snapshot -c sp0 rbd:rbd/fedora
    WARNING: Image format was not specified for 'rbd:rbd/fedora' and probing guessed raw.
             Automatically detecting the format is dangerous for raw images, write operations on block 0 will be restricted.
             Specify the 'raw' format explicitly to remove the restrictions.
    [root@ltczhp20 ~]# qemu-img snapshot -l rbd:rbd/fedora
    Snapshot list:
    ID        TAG                 VM SIZE                DATE       VM CLOCK
    sp0       sp0                     20G 1970-01-01 01:00:00   00:00:00.000

启动虚拟机，创建文件/root/hello.txt并写入字符串"hello world"，然后关闭虚拟机。

    [root@ltczhp20 ~]# qemu-system-s390x -nographic -enable-kvm -m 4G -drive format=raw,file=rbd:rbd/fedora
    
    In VM:
    
    [root@localhost ~]# echo "hello world" >> /root/hello.txt
    [root@localhost ~]# cat /root/hello.txt
    hello world
    
    [root@localhost ~]# halt

将虚拟机回滚到快照sp0，然后检查是否存在/root/hello.txt文件，如果不存在则说明快照已经成功回滚。

    [root@ltczhp20 ~]# qemu-img snapshot -a sp0 rbd:rbd/fedora
    
    [root@ltczhp20 ~]# qemu-system-s390x -nographic -enable-kvm -m 4G -drive format=raw,file=rbd:rbd/fedora
    
    In VM:
    
    [root@localhost ~]# ls /root/hello.txt
    ls: cannot access '/root/hello.txt': No such file or directory
    
    [root@localhost ~]# halt

删除快照：

    [root@ltczhp20 ~]# rbd snap ls rbd/fedora
    SNAPID NAME     SIZE
        22 sp0  20480 MB
    [root@ltczhp20 ~]# qemu-img snapshot -d sp0 rbd:rbd/fedora
    WARNING: Image format was not specified for 'rbd:rbd/fedora' and probing guessed raw.
             Automatically detecting the format is dangerous for raw images, write operations on block 0 will be restricted.
             Specify the 'raw' format explicitly to remove the restrictions.
    [root@ltczhp20 ~]# rbd snap ls rbd/fedora
    [root@ltczhp20 ~]# qemu-img snapshot -l rbd:rbd/fedora