---
title: 'Openstack Trove概要'
date: 2017-06-12 10:36:16
tags: [DBaaS,openstack,trove]
published: true
hideInList: false
feature: 
isTop: false
categories: 云计算
---

Trove简介
-------

Openstack Trove是openstack为用户提供的数据库即服务(DBaaS)。所谓DBaaS，即trove既具有数据库管理的功能，又具有云计算的优势。使用trove，用户可以：

*   "按需"获得数据库服务器
*   配置所获得的数据库服务器或者数据库服务器集群
*   对数据库服务器或者数据库服务器集群进行自动化管理
*   根据数据库的负载让数据库服务器集群动态伸缩

与openstack的其他组件一样，trove也提供RESTful API，并通过RESTful API和其他组件进行交互。

Trove架构
-------
Trove API和用户进行交互，当Trove API接收到用户请求时，trove API首先会调用Keystone的API来对用户进行认证，认证通过后才会去执行相应的操作。Trove API会同步处理操作简单的一些请求，复杂的请求则会通过Message Queue (RabbitMQ)交给Task Manager来处理。Task Manager会监听RabbitMQ的一个topic，收到请求后就会进行处理。这些请求通常是分配数据库实例、管理数据库实例的生命周期、操作数据库等。和openstack的其他组件一样，Trove也有一个Infrastructure Database来存储自己本身的数据，如数据库实例的信息等。Trove conductor的主要功能是接收来自guest agent的状态更新信息，这些信息会被存储在Infrastructure database里面或者作为调用结果返回给其他服务。通常这些状态更新信息包括：guest agent心跳包，数据库备份状态等。Guest Agent运营于数据库服务器中(虚拟机)，给trove其他组件提供了一套内部使用的API，trove的其他组件通过Message Queue来调用这些API，guest agent收到API调用请求后，执行相应的数据库操作。

Trove的安装部署请参考Openstack官方文档(多坑慎入)：

[https://docs.openstack.org/project-install-guide/database/ocata/](https://docs.openstack.org/project-install-guide/database/ocata/)

Trove的基本概念
----------

*   数据库实例（Instance）：包含数据库程序的openstack虚拟机，如果用户创建了一个数据库实例，那么他其实就创建了一台openstack虚拟机，并在该虚拟机上启动了数据库服务。
*   Datastore：用来表示和存储数据库的类型、版本、虚拟机镜像等信息。当用户创建一个数据库实例时需要指定Datastore.
*   配置组(Configuration Group)：数据库参数组成的集合。用户可以将配置组应用到一个或多个数据库实例上，因而避免了大量的重复操作。

Trove的用法
--------

### ０．添加Datastore

在创建数据库实例时，需要指定Datastore来告诉trove需要用到的镜像、数据库类型及版本信息。所以在创建数据库实例之前需要在系统中创建Datastore.

由于版权问题，openstack官方并没有提供可供下载的镜像，需要用户自己去build。可参考文档：

[https://docs.openstack.org/developer/trove/dev/building\_guest\_images.html](https://docs.openstack.org/developer/trove/dev/building_guest_images.html)

Build好镜像好需要将其上传到glance服务：

    ad@ltczhp11:~$ glance image-create --name mysql-5.6 --disk-format=qcow2 --container-format=bare --file=./mysql-5.6.qcow2 --visibility public 
    ad@ltczhp11:~$ glance image-list
    +--------------------------------------+--------------------------+
    | ID                                   | Name                     |
    +--------------------------------------+--------------------------+
    | 31d60001-c2bf-496e-9f21-069bb411bd3b | CouchDB                  |
    | eaf27f1b-3a8a-4efb-827c-697fa065933b | DB2                      |
    | 60ca8bfc-28a7-418a-9422-ec6548f23d54 | DIB-Mongodb              |
    | 51103b44-2618-4c15-8ded-4371bea8973a | DIB-Postgres             |
    | 8bc1a6a2-6ec3-4f37-bb54-91134b903996 | mongodb-lsl              |
    | c4c9a58f-bbe6-4640-8488-0c05b52028df | mysql-5.6                |
    | 5d800163-3660-467a-a433-e1827924a741 | PostgreSQL               |
    | 38291f8f-5af6-4338-82b7-3f18dc355cae | ubuntu16.04-server-s390x |
    +--------------------------------------+--------------------------+
    

使用trove-manage添加Datastore：

    root@ltczhp11:~# trove-manage datastore_update mysql ""　#创建一个datastore
    
    root@ltczhp11:~# trove-manage datastore_version_update mysql 5.6 mysql c4c9a58f-bbe6-4640-8488-0c05b52028df "" 1 #创建一个dadastore的版本，一个dadastore可以有多个版本
    
    root@ltczhp11:~# trove-manage datastore_update mysql 5.6　＃指定默认的datastore版本
    

### １．基本的数据库实例操作

*   列出所有的数据库实例
    $ trove list_
*   创建一个数据库实例
    $ trove create vm1 2 --size 3 --datastore  mysql_
*   重启一个数据库实例
    $ trove restart vm1_
*   删除一个数据库实例
    $ trove delete vm1_
*   强制删除一个数据库实例
    $ trove force-delete vm1_
*   调整一个数据库实例的规格
    $ trove resize-volume vm1 4_
    $ trove resize-instance vm1 3_

### 2．管理数据库用户

*   列出所有的数据库用户
    $ trove user-list_
*   创建一个用户
    $ trove user-create_
*   删除一个用户
    $ trove user-delete_
*   给一个用户授权访问某个数据库
    $ trove user-grant-access_
*   吊销用户对于某一个数据库的访问权限
    $ trove user-revoke-access_
*   显示用户信息
    $ trove user-show_
*   显示用户的权限
    $ trove user-show-access_

### 3\. 数据库管理

*   在一个数据库实例中创建一个数据库
    $ trove database-create_
*   列出一个数据库实例中的所有数据库
    $ trove database-list_
*   删除一个数据库实例中的某个数据库
    $ trove database-delete_

### 4\. 副本管理

*   创建一个副本
    $ trove create  --__replica_of_
*   将一个副本从源数据库分离
    $ trove detach-replica_
*   让一个副本在副本集合中成为源数据库
    $ trove promote-to-replica-source_
*   删除挂掉的源数据库
    $ trove eject-replica-source_
*   设置副本亲和性
    $ trove create  --__replica_of__  --locality affinity_
    $ trove create  --__replica_of__  --locality anti-affinity_