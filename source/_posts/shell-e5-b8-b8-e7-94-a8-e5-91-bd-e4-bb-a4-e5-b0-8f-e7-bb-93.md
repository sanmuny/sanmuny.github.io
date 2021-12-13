---
title: 'Shell常用命令小结'
date: 2014-04-24 09:41:21
tags: [shell]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

１、ls:这是linux里最常用的命令，像数学里的１一样，简单但很重要。类似于dos里的dir命令，该命令的功能是列出目录下的文件或子目录。

    -a:显示所有文件和目录，包括以.开头的隐藏文件
    -l:以长格式的形式显示

例如：

    kelvin@kelvin-laptop:~$ ls
    examples.desktop  公共的  模板  视频  图片  文档  下载  音乐  桌面
    
    kelvin@kelvin-laptop:~$ ls -a
    .                 .gconfd          .pulse-cookie
    ..                .gksu.lock       .recently-used.xbel
    .bash_history     .gnash           .sudo_as_admin_successful
    .bash_logout      .gnome2          .themes
    .bashrc           .gnome2_private  .thumbnails
    .bogofilter       .gstreamer-0.10  .update-notifier
    .cache            .gtk-bookmarks   .xsession-errors
    .compiz           .gvfs            .xsession-errors.old
    .config           .ICEauthority    公共的
    .dbus             .icons           模板
    .dmrc             .local           视频
    .esd_auth         .mozilla         图片
    .evolution        .nautilus        文档
    examples.desktop  .openoffice.org  下载
    .fontconfig       .profile         音乐
    .gconf            .pulse           桌面
    
    kelvin@kelvin-laptop:~$ ls -l
    总用量 36
    -rw-r--r-- 1 kelvin kelvin  179 2010-09-14 09:39 examples.desktop
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 公共的
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 模板
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 视频
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 图片
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 15:09 文档
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 14:57 下载
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 音乐
    drwxr-xr-x 2 kelvin kelvin 4096 2010-09-14 09:49 桌面
    

2、cd:选择当前工作目录。例如：

cd .. :回到上级目录。 cd 或 cd ~:返回家目录。 cd -:返回操作前目录

cd !$ :把上个命令的参数作为输入。

3、touch：创建一个或多个空文件。

4、pwd:显示当前工作目录。

5、cp:复制文件或目录

格式：cp 源 目的

    -r:复制目录
    -f:如遇同名文件或目录，强制覆盖目的文件或目录
    -p:保留文件或目录创建日期。
    

6、rm:删除文件或目录

    -r:如果是删除目录，需要加该选项。
    -f:强制删除
    

7、mv:移动文件或目录，一般用于重命名。

8、cat:显示全部的文件内容。

9、more:也是显示文件内容，一次只显示一屏幕，按空格显示下一页，按回车显示下一行，按q键退出。

10、less:显示文件内容，和more类似，区别在于less命令显示的文件可用方向键或pgup或pgdown控制。

11、head:显示文件头几行。如：head -3:显示头三行，默认为10行。

12、tail：显示文件后几行，与head类似。

13、ln:创建文件或目录的链接。源与目的都要使用绝对路径，且硬链接不能跨越磁盘分区。-s:创建软链接

14、mkdir:创建空目录。-p:可创建子目录。rmdir:删除空目录。-p:强制删除非空目录。

15、whereis:查看命令以及手册的位置。如：

    kelvin@kelvin-laptop:~$ whereis ls
    ls: /bin/ls /usr/share/man/man1/ls.1.gz
    

16、whatis:查看命令功能。

17、man：查看命令手册

18、find:查找文件或目录。例如：find /etc -name f*

19、locate:查找文件或目录。无需路径。需要注意的是，对于新创建的文件或目录，用updatedb更新数据库后才能用locate命令找到。

20、grep:查找文件中的内容。格式：grep 关键字 文件名

21、gzip:压缩文件。

-1:快速压缩 -9：最佳压缩

22、gunzip:解压文件。=gzip -d

23、tar:打包文件。

      -c:创建一个包。-v:显示详细过程。 -f：指定包名  -x:解包
    

可用该命令压缩打包，或解压缩解包：

    tar -zcvf file.tar.gz filename1 filename2 ……   
    tar -zxvf file.tar.gz