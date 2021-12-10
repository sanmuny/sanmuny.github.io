---
title: 'Kobject浅析'
date: 2014-04-24 09:23:20
tags: [kobject]
published: true
hideInList: false
feature: 
isTop: false
---

面向对象的思想的确在应用软件的开发中颇具优势，它让一个个纯逻辑的函数和数据变成了一个个有生命的个体。鉴于性能的考虑，系统软件的实现（例如linux kernel）并没有采用面向对象的语言（如C++、Java）。但这丝毫没有影响到用小c找对象。

简单来说，一个对象包含数据以及对这些数据的操作。如果把银行比作一个对象的话，银行里的RMB就是数据、而银行的工作人员就相当于对象中方法（即操作数据的结构）。如果，我们想打劫银行的话，我们只需要拿着枪指着工作人员说，“亲，给我拿500万出来”。当然，如果你是一个比较牛逼的劫匪你可能要1000万，或者更多。这没有关系，因为无论你要多少钱，你都不必亲自动手去取，工作人员会帮你办好这一切。当你拎着一袋子钱溜走的时候，你可能会想说，“自从有了面向对象，腰不酸了、腿不疼了，打劫也更有效率了”。

这个例子只是想说明面向对象的一个显而易见的优点——给劫匪，哦不，给用户提供了可直接使用的接口。当然，面向对象的优点可不只这一点。

kobject是Linux设备驱动模型的核心部分，它的作用是简单点说就是嵌入到设备和驱动相关的结构体之中。既可以将这些设备和驱动组织成树形结构，又可以让用户通过sysfs直接控制驱动和设备。让我们来搭讪一下kobject，从她那儿看看有对象的好处。

我们哲学老师在提问一个学生之前都会先问学生的家乡在哪儿。据他的逻辑应该是一方水土养一方人，从一个人的家乡可以大致推断出他的性格等。 不管这个理论对不对，我们先看看kobject的家乡——include/linux/kobject.h，kobject在这里定义：
```
    struct kobject {
        const char *name;
        struct list_head entry;
        struct kobject *parent;
        struct kset *kset;
        struct kobj_type *ktype;
        struct sysfs_dirent *sd;
        struct kref kref;
        unsigned int state_initialized:1;
        unsigned int state_in_sysfs:1;
        unsigned int state_add_uevent_sent:1;
        unsigned int state_remove_uevent_sent:1;
        unsigned int uevent_suppress:1;
    };
```  

每一个kobject实例都有一个名字name，用于标识这个实例。还有一个母亲parent，用于建立kobject的家族树。还有一个比较熟悉的面孔struct list_head entry，不用说，它的作用是把我们的kobject组织成双向链表。sd这个成员的作用和sysfs相关，kobject是组成sysfs树形结构的结点。kref是该结构体的引用计数，实质是一个原子型的整形量。下面几个unsigned int型的表示kobject的状态，kobject过得好还是不好，是不是党员，都在这里记录着。每一个kobject的结构体都有一个ktype，用来决定kobject被创建或销毁时应该如何处理：
```
    struct kobj_type {
        void (*release)(struct kobject *kobj);
        const struct sysfs_ops *sysfs_ops;
        struct attribute **default_attrs;
        const struct kobj_ns_type_operations *(*child_ns_type)(struct kobject *kobj);
        const void *(*namespace)(struct kobject *kobj);
    };
```

release是个回调函数，当kobject的引用计数为0时会被调用。当然，这个函数是需要我们自己完成的。其作用通常是释放kobject守护的结构体。每一类kobject都有自己的默认的属性，它放在结构体数组default\_attrs中。每一个属性都应该在sysfs中对应一个文件。而sysfs\_ops则存放着读写这些文件时的处理。

每一个kobject都可以属于某一个kset，一个kset就是一群kobject的集合。当然，物以类聚，人以群分，这些kobject要走到一起，当然是要有些共同特点的：
```
    struct kset {
        struct list_head list;
        spinlock_t list_lock;
        struct kobject kobj;
        const struct kset_uevent_ops *uevent_ops;
    };
```

kset是kobject的容器，也就是说，它把kobject又封装了一下。没一个kset结构体里面存放一个kobject，kset通过list连接起来，从而属于这一集合的所有kobj也连接起来了。uevent_ops这个结构体存放了对这一组object的操作。具体什么操作，咱下回分解。

kobject就好比每个人的守护天使，她通常不单独存在，而是作为其他结构体的成员出现，类似于struct list_head。在一个结构体中，找到嵌入于其中的kobject是很easy的事，就像领导找下属，随叫随到的那种。但是kobject要找到嵌入它的结构体的指针却没那么容易了。幸好，kernel给我们提供了一个可以做这件事的宏：

    contarner_of(pointer, type, member)
    

这个宏具体怎么用咱就不说了。google上百度一下，或者看下源码很容易就明白的。

有了这个宏，kobject守护的对象（设备和驱动）和kobject之间的联系就完全的建立起来了，kobject它妈叫它回家吃饭的时候就可以先找到设备和驱动，kobject要去打酱油的时候也可以叫上设备和驱动。

有了这个联系，系统里所有的设备和驱动就可以通过kobject给管理起来了。kobject和sysfs勾搭在一起就给用户层提供了修改设备和驱动参数的一种方式。

以下代码展示了如何利用kobject使用sysfs。
```
    /**
     * This is a simple module for kobject test.And it is licensed under the terms
     * of GNU/GPL v3 or later.
     * Wang Sen (C) <wangsen@linux.vnet.ibm.com>
     * 2012.3.23
     */
    #include <linux/module.h>
    #include <linux/init.h>
    #include <linux/kobject.h>
    #include <linux/sysfs.h>
    #include <linux/list.h>
    #include <linux/stat.h>
    #include <linux/string.h>
    
    #define BUF_SIZE 128
    
    MODULE_LICENSE("GPL");
    
    static char asset[BUF_SIZE] = "Hello Kitty";
    
    static void test_release(struct kobject *kobj);
    static ssize_t test_show(struct kobject *, struct attribute *, char *);
    static ssize_t test_store(struct kobject *, struct attribute *, const char *, size_t);
    
    static void test_release(struct kobject *kobj)
    {
    	if (kobj == NULL) {
    		return;
    	}
    	
    	printk("------[kobject_test]Hello,test_release is dancing\n");
    }
    
    static ssize_t test_show(struct kobject *kobj, struct attribute *attr, char *buf) 
    {
    	sprintf(buf, "asset:%s\nkobject_name:%s\n", asset,kobj->name);
    	return strlen(buf);
    }
    
    static ssize_t test_store(struct kobject *kobj, struct attribute *attr, const char *buf, size_t size) 
    {
    	if (strlen(buf) > BUF_SIZE) {
    		return E2BIG;
    	}
    	memset(asset, 0, BUF_SIZE);
    	sprintf(asset, "%s", buf);
    	return strlen(buf);
    }
    
    struct attribute test_attr = {
    	.name = "info",
    	.mode = S_IRWXUGO,
    }; 
    
    struct attribute test_attr_1 = {
    	.name = "addr",
    	.mode = S_IRWXUGO,
    }; 
    
    struct attribute *attr_array[] = {
    	&test_attr,
    	&test_attr_1,
    	NULL
    };
    
    struct sysfs_ops test_sysfs_ops = {
    	.show = test_show,
    	.store = test_store,
    };
    
    struct kobject test_kobject;
    struct kobj_type test_kobj_type= {
    	.release = test_release,
    	.sysfs_ops = &test_sysfs_ops,
    	.default_attrs = attr_array,
    };
    
    static int __init kobj_test_init(void)
    {
    	printk("------kobject init...");
    	kobject_init_and_add(&test_kobject, &test_kobj_type, NULL, "test_object_of_kelvin");
    	return 0;
    }
    
    static void __exit kobj_test_exit(void)
    {
    	kobject_put(&test_kobject);
    	printk("------See you");
    }
    
    module_init(kobj_test_init);
    module_exit(kobj_test_exit);
    
    MODULE_AUTHOR("Wang Sen <senwang@linux.vnet.ibm.com>");
```

参考文献：

* 《LINUX设备驱动程序》 第三版 Jonathan Corbet,Alessandro Rubini,Greg Kroah-hartman 
* [http://linux.chinaunix.net/techdoc/system/2008/07/19/1018480.shtml](http://linux.chinaunix.net/techdoc/system/2008/07/19/1018480.shtml) 
* [http://blog.21ic.com/user1/5593/archives/2010/69112.html](http://blog.21ic.com/user1/5593/archives/2010/69112.html) 
* [http://blog.csdn.net/yili_xie/article/details/5835935](http://blog.csdn.net/yili_xie/article/details/5835935)