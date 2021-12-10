---
title: '使用mock来编译和管理RPM软件包'
date: 2014-02-13 17:23:52
tags: []
published: true
hideInList: false
feature: 
isTop: false
---

**buildroot**

在打包时用到的spec文件中包含一些tag，这些对大小写不敏感的tag用冒号来定义。BuildRoot就是其中的一个tag。例如，在libvirt的spec文件中BuildRoot的定义如下：

`BuildRoot: %{_tmppath}/%{name}-%{version}-%{release}-root`

BuildRoot设置的目录会被当作临时的根目录，%install部分安装的文件都会被临时安装到这里。之所以引入BuildRoot是为了在打包过程中不影响现在的系统。 RPM宏设定的BuildRoot默认值是~/rpmbuild/BUILDROOT, 可以在spec文件中设置该tag，或者在rpmbuild命令执行时使用- -buildroot选项来指定。

**mock的功能**

mock不只是将文件安装到Buildroot指定的根目录，而是创建一个打包的沙盒(sandbox)，挂载一些必要的文件系统(proc，sys等)，将打包过程所用到的软件包(BuildRequires指定)都安装到沙盒中，然后将指定的SRPM包进行编译，生成最终的RPM包。mock不仅可以使打包过程变得整洁，更能够测试BuildRequires是否完整。除了打包之外，mock也可以用来制作沙盒来测试软件包。

**安装mock**

使用YUM安装fedora维护者工具fedora-packager后，mock和koji作为依赖也被安装到系统中了。执行以下命令即可：

`sudo yum install fedora-packager`

**简单的配置mock**

在使用mock之前需要将当前用户加入到mock用户组，并使用户登陆到该用户组：

`sudo usermod -a -G mock [User name] && newgrp mock`

**使用mock来打包**

使用mock打包需要配置文件来指定安装软件包所用到的YUM仓库，/etc/mock目录下有许多这样的配置文件。配置文件可以通过-r选项来指定，如果没有指定，则使用默认的配置文件/etc/mock/default.cfg。

`mock libvirt-1.2.2-1.fc20.src.rpm`

将会在BUILDROOT目录下挂载一些必要的文件必要的文件系统，并安装打包过程需要用到的软件包，最终生成RPM包。