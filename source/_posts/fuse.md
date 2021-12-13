---
title: 'FUSE'
date: 2014-04-24 12:58:53
tags: []
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

### **一、FUSE简介**

FUSE（用户空间文件系统）是这样一个框架，它使得FUSE用户在用户态下编写文件系统成为可能，而不必和内核打交道。FUSE由三个部分组成，linux内核模块、FUSE库以及mount 工具。用户关心的只是FUSE库和mount工具，内核模块仅仅提供kernel的接入口，给了文件系统一个框架，而文件系统本身的主要实现代码位于用户空间中。 FUSE库给用户提供了编程的接口，而mount工具则用于挂在用户编写的文件系统。

FUSE起初是为了研究AVFS(A Virtual Filesystem)而设计的，而现在已经成为SourceForge的一个独立项目，目前适用的平台有Linux, FreeBSD, NetBSD, OpenSolaris和Mac OS X。官方的linux kernel版本到2.6.14才添加了FUSE模块，因此2.4的内核模块下，用户如果要在FUSE中创建一个文件系统，需要先安装一个FUSE内核模块，然后使用FUSE库和API来创建。

### **二、FUSE特性**

*   库文件和 API简单，极大地方便了用户的使用
*   安装简便，不需要加补丁或者重新编译 kernel
*   执行安全，使用稳定
*   高效，相对于其它用户态文件系统实例
*   非特权用户可以使用
*   基于 linux2.4.x 和 2.6.x 内核，现在可以支持JavaTM 绑定，不必限定使用C和C++来编 写文件系统

### **三、源代码目录**

*   ./doc 包含FUSE相关文档
*   ./include 包含了FUSE API头，对创建文件系统有用，主要用fuse.h
*   ./lib 存放FUSE库的源代码
*   ./util 包含了FUSE工具库的源代码
*   ./example 参考的例子

### **四、安装**

FUSE的源码安装类似于其他软件，只需要在FUSE的源码目录下执行如下命令即可：

    ./configure
    make
    make install（以root身份执行）
    

### **五、FUSE operations**

FUSE使用fuse_operations来给用户提供编程结构，让用户通过注册自己编写的函数到该结构体来实现自己的文件系统。

    struct fuse_operations {
        int (*getattr) (const char *, struct stat *);
        int (*readlink) (const char *, char *, size_t);
        int (*mknod) (const char *, mode_t, dev_t);
        int (*mkdir) (const char *, mode_t);
        int (*unlink) (const char *);
        int (*rmdir) (const char *);
        int (*symlink) (const char *, const char *);
        int (*rename) (const char *, const char *);
        int (*link) (const char *, const char *);
        int (*chmod) (const char *, mode_t);
        int (*chown) (const char *, uid_t, gid_t);
        int (*truncate) (const char *, off_t);
        int (*utime) (const char *, struct utimbuf *);
        int (*open) (const char *, struct fuse_file_info *);
        int (*read) (const char *, char *, size_t, off_t, struct fuse_file_info *);
        int (*write) (const char *, const char *, size_t, off_t, struct fuse_file_info *);
        int (*statfs) (const char *, struct statvfs *);
        int (*flush) (const char *, struct fuse_file_info *);
        int (*release) (const char *, struct fuse_file_info *);
        int (*fsync) (const char *, int, struct fuse_file_info *);
        int (*setxattr) (const char *, const char *, const char *, size_t, int);
        int (*getxattr) (const char *, const char *, char *, size_t);
        int (*listxattr) (const char *, char *, size_t);
        int (*removexattr) (const char *, const char *);
        int (*opendir) (const char *, struct fuse_file_info *);
        int (*readdir) (const char *, void *, fuse_fill_dir_t, off_t, struct fuse_file_info *);
        int (*releasedir) (const char *, struct fuse_file_info *);
        int (*fsyncdir) (const char *, int, struct fuse_file_info *);
        void *(*init) (struct fuse_conn_info *conn);
        void (*destroy) (void *);
        int (*access) (const char *, int);
        int (*create) (const char *, mode_t, struct fuse_file_info *);
        int (*ftruncate) (const char *, off_t, struct fuse_file_info *);
        int (*fgetattr) (const char *, struct stat *, struct fuse_file_info *);
        int (*lock) (const char *, struct fuse_file_info *, int cmd, struct flock *);
        int (*utimens) (const char *, const struct timespec tv[2]);
        int (*bmap) (const char *, size_t blocksize, uint64_t *idx);
    };
    

### **六、hello示例文件系统源码分析**

FUSE在源码目录example下有一些示例文件系统，通过阅读这些示例文件系统可以掌握FUSE用户态文件系统的编写规范。下面以hello.c为例分析FUSE的编写规范：

    #define FUSE_USE_VERSION 26
    #include <fuse.h>
    #include <stdio.h>
    #include <string.h>
    #include <errno.h>
    #include <fcntl.h>
    
    static const char *hello_str = "Hello World!\n";
    static const char *hello_path = "/hello";
    

/_该函数与stat()类似，用于得到文件的属性，将其存入到结构体struct stat中_/

    static int hello_getattr(const char *path, struct stat *stbuf)
    {
        int res = 0;
        memset(stbuf, 0, sizeof(struct stat));	 //用于初始化结构体stat
        if (strcmp(path, "/") == 0) {
        stbuf->st_mode = S_IFDIR | 0755;	 //S_IFDIR 用于说明/为目录，详见S_IFDIR定义
        stbuf->st_nlink = 2;	 //文件链接数
    } else if (strcmp(path, hello_path) == 0) {
        stbuf->st_mode = S_IFREG | 0444;	//S_IFREG用于说明/hello为常规文件
        stbuf->st_nlink = 1;
        stbuf->st_size = strlen(hello_str);	 //设置文件长度为hello_str的长度
    } else
        res = -ENOENT;	 //返回错误信息，没有该文件或目录
        return res;	 //执行成功返回0
    }
    

/_该函数用于读取/目录中的内容，并在/目录下增加了. .. hello三个目录项_/

    static int hello_readdir(const char *path, void *buf, fuse_fill_dir_t filler,  off_t offset, struct fuse_file_info *fi)    
    {    
        (void) offset;        
        (void) fi;        
        if (strcmp(path, "/") != 0)     
            return -ENOENT;
    /*
    fill的定义：
    typedef int (*fuse_fill_dir_t) (void *buf, const char *name,const struct stat *stbuf, off_t off);
    其作用是在readdir函数中增加一个目录项
    */
        filler(buf, ".", NULL, 0);
        //在/目录下增加. 这个目录项
        filler(buf, "..", NULL, 0);
        // 增加.. 目录项
        filler(buf, hello_path + 1, NULL, 0);
        //增加hello目录项
        return 0;
    }
    

/_用于打开hello文件_/

    static int hello_open(const char *path, struct fuse_file_info *fi)
    {
        if (strcmp(path, hello_path) != 0) 
            return -ENOENT;
        if ((fi->flags & 3) != O_RDONLY)
            return -EACCES;
        return 0;
    }
    

/_读取hello文件时的操作，它实际上读取的是字符串hello_str的内容_/

    static int hello_read(const char *path, char *buf, size_t size, off_t offset, struct fuse_file_info *fi)
    {
        size_t len;
        (void) fi;
        if(strcmp(path, hello_path) != 0)
            return -ENOENT;
        len = strlen(hello_str);
        if (offset < len) {
            if (offset + size > len)
                size = len - offset;
            memcpy(buf, hello_str + offset, size);
        } else
        size = 0;
        return size;
    }
    

/_注册上面定义的函数_/

    static struct fuse_operations hello_oper = {
        .getattr = hello_getattr,
        .readdir = hello_readdir,
        .open = hello_open,
        .read = hello_read,
    };
    

/_用户只需要调用fuse_main()，剩下的事就交给FUSE了_/

    int main(int argc, char *argv[])
    {
        return fuse_main(argc, argv, &hello_oper, NULL);
    }
    

终端运行：

    ~/fuse/example$ mkdir /tmp/fuse	 //在/tmp下建立fuse目录，用于挂载hello文件系统
    ~/fuse/example$ ./hello /tmp/fuse	 //挂载hello文件系统
    ~/fuse/example$ ls -l /tmp/fuse	//执行ls时，会调用到readdir函数，该函数会添加一个hello	 文件
    总用量 0
    
    -r--r--r-- 1 root root 13 1970-01-01 07:00 hello
    
    ~/fuse/example$ cat /tmp/fuse/hello	//执行cat hello时，会调用open以及read函数，将
    Hello World!	 字符串hello_str中的内容读出
    ~/fuse/example$ fusermount -u /tmp/fuse //卸载hello文件系统
    

通过上述的分析可以知道，使用FUSE必须要自己实现对文件或目录的操作，系统调用也会最终调用到用户自己实现的函数。用户实现的函数需要在结构体fuse\_operations中注册。而在main()函数中，用户只需要调用fuse\_main()函数就可以了，剩下的复杂工作可以交给FUSE。