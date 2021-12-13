---
title: '基于Linux与lpc3250开发板的交叉开发环境搭建'
date: 2014-04-24 14:35:09
tags: []
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

###**一、Bootloader的安装（在windows下进行）**

1、什么是Bootloader:

要想弄明白什么是Bootloader，我们先从PC上的bootloader说起。PC上的BIOS和硬盘上的引导记录有着和嵌入式开发板中的bootloader类似的作用。PC的Bootloader由BIOS和MBR组成，BIOS固化在主板的一个芯片上，MBR则是硬盘的主引导扇区的缩写。PC启动后，首先执行BIOS的启动程序，根据用户的COMS设置，BOIS加载硬盘MBR的启动数据，并把系统的控制权交给保存在MBR中的OS Loader（如grub），最后再由OS Loader将控制权交给OS内核。

了解了什么是PC中的Bootloader，我们再来看什么是嵌入式系统中的Bootloader。嵌入式系统中没有与BIOS类似的芯片，这就需要开发人员自己设计Boootloader。不过，我们不必从零开始写这些代码，已经有公司和组织为大多数嵌入式系统写好了Bootloader。

2、lpc3250的Bootloader组成：

*   kickstart：位于Flash的Block0，负责加载从Flash Block1开始的程序，这里只的是S1L。开发板上电后，kickstart被内部的IROM加载并执行。IROM只能加载Block1以内的映像，而kickstart被加载后将被允许加载从Flash Block1开始的多个Block的映像文件。所以，kickstart上电后，kickstart加载S1L，也可以直接加载放在Block1的应用程序。
*   S1L：对芯片和板子进行初始化，并提供一个用于应用程序开发和执行控制的监控程序。其常用功能如下： @擦写nandFlash @串口下载，SD卡下载 @设置CPU频率 @设置从SD卡，NandFlash启动 @加载引导Eboot
*   Eboot：设置内核在NandFlash中的位置，内核复制到RAM的位置，以及内核的大小；网络下载
*   Uboot：操作系统引导。Uboot的具体分析留到以后再说。

3、安装步骤：

*   由于笔记本不带串口，所以第一步是找个usb串口连接线，并安装好驱动。
*   将开发板的电源线连接好，然后连接开发板串口与PC上的usb串口。
*   利用开发板所带光盘中的smartARM3250\_boot.exe安装kickstart和S1L：进入到光盘中smartARM3250\_boot.exe所在的目录，确保kickstart.bin和stage1.bin也在该目录下。运行smartARM3250_boot.exe。首先选择好串口，我的是com3。如果你不知道你的串口是多少的话，可以在右键单击我的电脑——》管理——》设备管理器。查看到自己的串口位置后，点击打开串口。点击装载Bootloader，软件会提示你短接jp6，点击确定，短接jp6后reset开发板，过几秒钟便提示装载成功。这儿的bootloader不是我们上面所讲的bootloader，它指的是bootloader.bin，我们

执行了上面过程后，smartARM3250\_boot.exe就将bootloader.bin拷贝到开发板执行，其实我们可以把bootloader理解成smartARM3250\_boot.exe的客户端软件。

装载bootloader.bin后，我们下来就正式安装kickstart了。在右下脚的Flash擦出中选择编程NandFlash， 块地址设为0，点击选择文件，选择kickstart.bin。点击编程。这样我们就把kickstart.bin装载到了NandFlash的Block0。用同样的方法我们可以装载S1L，只是要将块地址改为1。

*   安装U-boot或Eboot:

a. 打开windows的超级终端，配置好参数后连接。reset开发板，进入到SmartArm3250的工作台，将光盘中的u-boot.bin或eboot.nb0拷贝到一张SD卡上，然后将SD卡插入到开发板的SD插槽中，在超级终端中输入命令：load blk u-boot.bin(eboot.nb0) raw 0x83fc0000，将u-boot.bin或eboot.nb0加载到SDRAM中。 b. 在超级终端中敲入命令：nsave。将u-boot.bin(eboot.nb0)写入到NANDFlash。 c. 最后输入命令：aboot flash raw 0x83fc0000，设置从NANDFlash启动U-boot(Eboot）。

###**二、Linux系统（Ubuntu）下所需要的软件的安装步骤：**

1、交叉工具链的安装：

a、什么是交叉工具链：在PC机上开发嵌入式软件所需要的编译器、make等工具的集合。

b、安装步骤：

*   将光盘中的tc-nxp-lnx-armvfp-4.3.2-1.i386.rpm拷贝到PC机的桌面。
    
*   打开终端，输入sudo rpm --force-debian -ivh tc-nxp-lnx-armvfp-4.3.2.1-1.i386.rpm命令。
    
*   执行上述命令后，就将交叉工具链安装到/opt/nxp目录中了，现在我们将安装路径加入到PATH变量中去。在终端中输入命令：
    
    gedit ~/.bashrc
    

在打开的.bashrc文件的末尾添加如下语句：

    export PATH="$PATH:/$HOME/bin:/opt/nxp/gcc-4.3.2-glibc-2.7/bin"
    

保存关闭，注销当前用户，重新登录

*   检测是否安装好交叉工具链：在终端中输入arm-vfp-linux-gnu-并按TAB键，如果能看到很多arm-vfp-linux-gnu-为前缀的命令，则说明交叉开发工具链已经安装好了。

2、NFS服务器的安装：

(NFS的详细介绍请参考[NFS](http://blog.csdn.net/sailor_8318/archive/2008/04/15/2295495.aspx))

a、NFS的功能：

NFS是网络文件系统的缩写，它的功能是把NFS服务器（即Linux主机）的某个目录挂载到开发板的文件系统上（开发板上Linux系统的安装我们在后面会讲到），这样，开发板就可以执行该目录中的可执行程序。这样做的优点在于，不用将程序写入开发板的Flash，减少了对Flash的损害。

b、NFS的安装：

在Ubuntu下的安装很easy：　sudo　apt-get install nfs-sever

3、TFTP服务器的安装：

a、什么是tptp：TFTP是远程文件传输协议的缩写，其作用是将主机中设定目录下的文件拷贝到开发板的文件系统中，它与NFS的区别显而意见。

b、安装：还是很easy：sudo apt-get install tftp-hpa tftpd-hpa xinetd。第一个是客户端程序，第二个是服务器端程序，第三个是守护进程。

c、Ubuntu系统在安装完成后自动启动tftp服务，也可以通过命令：

    sudo service xinetd start或restart命令启动。
    

d、然后进入xinetd.d文件夹（cd /etc/xinetd.d），查看是否有一个tftp文件，如果没有就新建一个，如果有的话就查看内容是否与下面的一致，不一致则修改，内容如下：

    service tftp  
    {  
        socket_type = dgram  
        wait = yes  
        disable = no  
        user = root  
        protocol = udp  
        server = /usr/sbin/in.tftpd  
        server_args = -s /home/tftpboot  
        log_on_success += PID HOST DURATION  
        log_on_failure += HOST  
    }  
    

e、然后保存关闭。输入命令：sudo /etc/init.d/xinetd restart重启服务。

TFTP的使用我们到后面在介绍。

4、minicom的安装和配置：

a、minicom的功能：就四个字，超级终端

b、安装：sudo apt-get install minicom

c、配置：在终端输入：sudo minicom -s选择串口设置，串口设备设为/dev/ttyUSB0（也许你的不一样，可以在dev目录下查看）,波特率设为115200，硬件流和软件流控制都设为No。回车退回到刚进入时的界面，选择save setup as dfl。

###**三、Linux内核，安全文件系统和根文件系统的安装：**

1、连接好串口线和网线。

2、插入光盘，将光盘中的uImage文件拷贝到/var/lib/tftpboot目录下。

3、在主机终端中输入：sudo chmod -R 777 /var/lib/tftpboot 命令来改变该目录权限。（这个目录是tftp服务器默认存放要传输文件的目录）

4、打开另一个终端，输入命令：sudo minicom

5、reset开发板，这时终端就进入了U-boot的工作台。

5、在工作台中输入命令：tftp 80008000 uImage将内核镜像文件拷贝到开发板内存中。

6、在工作台中输入命令：nand write.jffs2 0x80008000 0x00200000 $(filesize)将镜像文件从内存中拷贝到NANDFlash相应的位置。

7、在工作台中输入命令：setenv kernelsize $(filesize) 设置内核大小为镜像文件大小。

8、在工作台中输入命令：saveenv 保存设置

9、安装安全文件系统：

*   将光盘中的safefs.cramfs文件放到/var/lib/tftpboot目录下。
    
*   在工作台中输入命令：
    
    tftp 80008000 safefs.cramfs nand erase clean 0x00600000 $(filesize)  
    nand write.jffs2 0x80008000 0x00600000 $(filesize)
    

10、将光盘中的rootfs.tar.bz2文件和burn文件拷贝到一张SD卡上。

11、将SD卡插入开发板插槽中，在工作台输入命令：run safemode。

12、坐等烧写完成，reset开发板。