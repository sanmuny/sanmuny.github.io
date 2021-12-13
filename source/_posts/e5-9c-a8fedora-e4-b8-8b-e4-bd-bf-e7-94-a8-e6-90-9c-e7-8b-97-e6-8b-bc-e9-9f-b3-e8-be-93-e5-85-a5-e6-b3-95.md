---
title: '在fedora下使用搜狗拼音输入法'
date: 2016-04-07 12:14:19
tags: [fcitx,搜狗拼音输入法]
published: true
hideInList: false
feature: 
isTop: false
categories: 开发工具
---

Linux下的拼音输入法实在是不敢恭维，还好有人把搜狗拼音输入法制作成了RPM包．安装此rpm包就可以在Linux下面使用搜狗拼音输入法及其字库了．

第一步，下载RPM包．

百度网盘地址：[http://pan.baidu.com/s/1bpblFoN](http://pan.baidu.com/s/1bpblFoN)

第二步，安装RPM包.

    $sudo yum install  fcitx-sogoupinyin-0.0.4-1.fc20.x86_64.rpm  //注意输入正确的路径
    

第三步，卸载ibus.

    $sudo yum remove ibus

第四步，设置fcitx开机自动启动.

    $sudo yum install gnome-tweak-tool
    $gnome-tweak-tool  //在开机启动一项中添加fcitx即可

第五步，重启gnome

    $gnome-session-quit

最后，使用Ctrl+space愉快的玩耍.