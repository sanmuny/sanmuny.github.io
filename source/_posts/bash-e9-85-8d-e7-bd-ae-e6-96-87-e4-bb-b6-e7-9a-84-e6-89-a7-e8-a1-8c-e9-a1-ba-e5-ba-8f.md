---
title: 'bash配置文件的执行顺序'
date: 2014-03-19 11:09:05
tags: [shell]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

Linux用户在登陆系统之后会启动shell，并按照一定顺序读取shell的配置文件。以bash为例，配置文件的读取顺序如下：

1.  /etc/profile
    
2.  如果是图形界面登陆系统，读取~/.profile，bash配置完毕。
    
3.  如果是命令行或者ssh登陆系统，读取~/.bash_profile，bash配置完毕。
    
4.  如果是命令行或者ssh登陆系统，且~/.bash\_profile不存在，读取~/.bash\_login，bash配置完毕。
    
5.  如果是命令行或者ssh登陆系统，且~/.bash\_profile,~/.bash\_login不存在，读取~/.profile，bash配置完毕。
    

图形界面启动后，用户可能会再启动一个shell，该shell的配置文件是~/.bashrc，用户自定义的配置一般会放到这里。