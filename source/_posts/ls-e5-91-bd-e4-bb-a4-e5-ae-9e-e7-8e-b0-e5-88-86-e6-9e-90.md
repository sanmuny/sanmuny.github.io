---
title: 'ls命令实现分析'
date: 2014-04-24 14:21:55
tags: [shell]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

###**一、ls命令的功能分析**

使用man ls命令查看ls命令手册（功能描述和主要选项摘录如下）：

List information about the FILEs (the current directory by default).Sort entries alphabetically if none of -cftuvSUX nor --sort.

列出文件（默认当前目录）信息，如果没有-cftuvSUX和--sort选项，就按照字母顺序排序。

    -a, --all   do not ignore entries starting with .：不忽略以.开始的隐藏文件
    
    -A, --almost-all   do not list implied . and ..：不列出 .（当前目录）和..（上级目录）
    
    -B, --ignore-backups  do not list implied entries ending with ~：忽略以～结尾的备份文件
    
    -c with -lt: sort by, and show, ctime (time of last modification of file status information) with -l: show ctime and  sort  by  name otherwise: sort by ctime：和-lt一起使用，则显示ctime（最后修改文件信息的时间），并按ctime排序显示；和-l一起使用，则显示
    ctime，但只按文件名的字母顺序排序；其他，按ctime排序显示。/*该选项和-t选项在单独使用的时候是等价的，但在和-l选项配合使用的时候，-c的功能会被屏蔽，而-t选项不会*/
    
    -d, --directory   list directory entries instead of contents, and do not  dereference symbolic links：不是列出该目录的文件信息，而是列出该目录项。不追踪符号链接的实际位置。
    
    -F, --classify   append indicator (one of */=>@|) to entries：在每个entry后面加上标识文件内容的符号:
    
             * : 标识可执行文件     / : 标识目录          = : 套接字文件
             @ : 符号链接文件       | : 管道文件
    
    -l     use a long listing format：以长格式显示。
    

###**二、ls所用到的系统调用：**

使用strace ls命令我们可以查看ls命令使用到的系统调用，其中最重要的几个为：

    open(".", O_RDONLY|O_NONBLOCK|O_LARGEFILE|O_DIRECTORY|O_CLOEXEC) = 3
    getdents64(3, /* 68 entries */, 32768)  = 2240
    getdents64(3, /* 0 entries */, 32768)   = 0
    close(3)                                = 0
    

1、open系统调用：

打开当前目录文件，返回获得的文件描述符。

*   O\_RDONLY：只读 O\_NONBLOCK：以非阻塞的方式打开文件 O_LARGEFILE：允许打开大文件
*   O\_DIRECTORY：如果路径不是目录，则打开错误 O\_CLOEXEC：在创建新的进程后关闭文件描述符

2、close系统调用：

关闭文件描述符。

3、getdents64：

读取当前目录下的文件。

三、getdents64的系统调用服务例程：

由于getdents64实现了ls核心功能，下面着重分析getdents64系统调用在内核态下的实现。getdents64在fs/readdir.c中定义如下：
```
     275SYSCALL_DEFINE3(getdents64, unsigned int, fd,
     276                struct linux_dirent64 __user *, dirent, unsigned int, count)
     277{
     278        struct file * file;
     279        struct linux_dirent64 __user * lastdirent;
     280        struct getdents_callback64 buf;
     281        int error;
     282
     283        error = -EFAULT;
     284        if (!access_ok(VERIFY_WRITE, dirent, count))
     285                goto out;
     286
     287        error = -EBADF;
     288        file = fget(fd);
     289        if (!file)
     290                goto out;
     291
     292        buf.current_dir = dirent;
     293        buf.previous = NULL;
     294        buf.count = count;
     295        buf.error = 0;
     296
     297        error = vfs_readdir(file, filldir64, &buf);
     298        if (error >= 0)
     299                error = buf.error;
     300        lastdirent = buf.previous;
     301        if (lastdirent) {
     302                typeof(lastdirent->d_off) d_off = file->f_pos;
     303                if (__put_user(d_off, &lastdirent->d_off))
     304                        error = -EFAULT;
     305                else
     306                        error = count - buf.count;
     307        }
     308        fput(file);
     309out:
     310        return error;
     311}
```

getdents64首先调用fget函数得到目录文件的file结构体，再调用虚拟文件系统提供的vfs_readdir函数，读取目录项，该函数的定义也在fs/readdir64中：
```
    int vfs_readdir(struct file *file, filldir_t filler, void *buf)
      24{
      25        struct inode *inode = file->f_path.dentry->d_inode;
      26        int res = -ENOTDIR;
      27        if (!file->f_op || !file->f_op->readdir)
      28                goto out;
      29
      30        res = security_file_permission(file, MAY_READ);
      31        if (res)
      32                goto out;
      33
      34        res = mutex_lock_killable(&inode->i_mutex);
      35        if (res)
      36                goto out;
      37
      38        res = -ENOENT;
      39        if (!IS_DEADDIR(inode)) {
      40                res = file->f_op->readdir(file, buf, filler);
      41                file_accessed(file);
      42        }
      43        mutex_unlock(&inode->i_mutex);
      44out:
      45        return res;
      46}
```  

该函数首先通过file结构体得到inode，然后从inode中获得并执行file\_operations结构体中的读取目录函数（底层文件系统提供）file->f\_op->readdir(file, buf, filler)。

综上所述，实际上对文件进行操作的是底层文件系统提供的函数，它通过file_operations结构体可被上层的虚拟文件系统调用，而用户程序又可通过系统调用进入内核态，调用虚拟文件系统提供的接口函数。