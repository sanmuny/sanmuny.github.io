---
title: '使用gerrit作为代码评审工具'
date: 2015-06-11 15:39:59
tags: [devops,gerrit]
published: true
hideInList: false
feature: 
isTop: false
categories: 开发工具
---

### 需求描述

其实作为项目代码的maintainer，一直习惯于mailing list + git的代码评审及管理，无奈公司主推敏捷+devops，老板让改用gerrit。硬着头皮切换到gerrit，在这里记录下安装配置的过程及踩过的许多坑，以便网友们以后配置gerrit留作参考。

需求其实很简单，我们项目一直使用公司内部一个类似于github的代码托管网站来托管项目代码，使用邮件列表来评审代码。代码通过评审通过后，我再将patch push到代码托管服务器上去。整个开发流程如下图所示：

现在需要切换到gerrit来作为代码评审工具，以便于能够和jenkins集成，搭建一个集开发、构建、测试、部署为一体的devops系统，结构如下图所示。本文只关注gerrit的搭建。

### Gerrit简介

### 安装步骤

1 . 安装Java.

网上有很多安装java的博客和文章，因此在这里不再赘述，可以参考下面这篇文章：

[Linux下安装java](http://tonyty163.blog.51cto.com/721698/463052)

2 . 给Gerrit单独创建一个账户

    #useradd gerrit
    #passwd gerrit
    #su gerrit
    

3 . 下载gerrit

gerrit是在google上托管的项目，翻墙下载比较麻烦，可以在这里下载2.11版本的gerrit：

[百度网盘下载Gerrit](http://pan.baidu.com/s/1i3rfNDz)

将网盘中的两个文件gerrit-2.11.war以及bcpkix-jdk15on-151.jar下载到gerrit用户的家目录/home/gerrit下。

4 . 按下面的步骤安装gerrit，其中的问题大部分可以敲回车选择默认设置。

    
    [gerrit@linux ~]$ java -jar ./gerrit-2.11.war init -d gerrit 
        
    //-d指定gerrit的安装目录
    
    Using secure store: com.google.gerrit.server.securestore.DefaultSecureStore
    
    *** Gerrit Code Review 2.11
    *** 
    
    Create '/home/gerrit/gerrit'   [Y/n]? y
    
    *** Git Repositories
    *** 
    
    Location of Git repositories   [git]:  
    
    *** SQL Database
    *** 
    
    Database server type           [h2]: 
    
    *** Index
    *** 
    
    Type                           [LUCENE/?]: 
    
    *** User Authentication
    *** 
    
    Authentication method          [OPENID/?]: http    //为了不依赖于openid我们这里要设置成http
    Get username from custom HTTP header [y/N]? y
    Username HTTP header           [SM_USER]: 
    SSO logout URL                 : 
    
    *** Review Labels
    *** 
    
    Install Verified label         [y/N]? 
    
    *** Email Delivery
    *** 
    
    SMTP server hostname           [localhost]: 
    SMTP server port               [(default)]: 
    SMTP encryption                [NONE/?]: 
    SMTP username                  : 
    
    *** Container Process
    *** 
    
    Run as                         [gerrit]: 
    Java runtime                   [/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.45-38.b14.fc21.x86_64/jre]: 
    Copy gerrit-2.11.war to /home/gerrit/gerrit/bin/gerrit.war [Y/n]? 
    Copying gerrit-2.11.war to /home/gerrit/gerrit/bin/gerrit.war
    
    *** SSH Daemon
    *** 
    
    Listen on address              [*]: 
    Listen on port                 [29418]: 
    
    Gerrit Code Review is not shipped with Bouncy Castle Crypto SSL v151
      If available, Gerrit can take advantage of features
      in the library, but will also function without it.
    Download and install it now [Y/n]? n  //选择n,因为这个文件已经下载好了，安装完手动拷贝进去就行
    Generating SSH host key ... rsa(simple)... done
    
    *** HTTP Daemon
    *** 
    
    Behind reverse proxy           [y/N]? y  //需要配置反向代理来满足http认证
    Proxy uses SSL (https://)      [y/N]? N
    Subdirectory on proxy server   [/]: 
    Listen on address              [*]: 
    Listen on port                 [8081]: 
    Canonical URL                  [http://null/]: 
    
    *** Plugins
    *** 
    
    Installing plugins.
    Install plugin download-commands version v2.11 [y/N]? 
    Install plugin reviewnotes version v2.11 [y/N]? 
    Install plugin singleusergroup version v2.11 [y/N]? 
    Install plugin replication version v2.11 [y/N]? 
    Install plugin commit-message-length-validator version v2.11 [y/N]? 
    Initializing plugins.
    No plugins found with init steps.
    
    Initialized /home/gerrit/gerrit
    Executing /home/gerrit/gerrit/bin/gerrit.sh start
    Starting Gerrit Code Review: OK
    

安装完成后需要将下载好的bcpkix-jdk15on-151.jar文件拷贝到安装目录中去：

    cp bcpkix-jdk15on-151.jar /home/gerrit/gerrit/lib/bcpkix-jdk15on-151.jar
    

5 . 配置Apache反向代理

修改httpd的配置文件，在其末尾加入：
```
    <VirtualHost *>
      ServerName gerrit.example.com
    
      ProxyRequests Off
      ProxyVia Off
      ProxyPreserveHost On
    
      <Proxy *>
        Order deny,allow
        Allow from all
      </Proxy>
      <Location /login/>
        AuthType Basic
        AuthName "Gerrit Code Review"
        AuthBasicProvider file
        AuthUserFile /home/gerrit/gerrit/etc/passwords
        Require valid-user
      </Location>
      ProxyPass / http://9.181.129.109:8081/
    </VirtualHost>
```  

需要将IP地址替换成你自己的IP.修改/etc/hosts，加入gerrit.example.com的解析：

    9.181.129.109	gerrit.example.com
    

这里也需要替换IP地址。

6 . 创建管理员账户，Gerrit把第一个被创建的用户当做是管理员账户：

    $htpassword /home/gerrit/gerrit/etc/passwords $username
    

$username替换成你要设置的用户名。

7 . 重启服务

    $./gerrit/bin/gerrit.sh restart
    $sudo service httpd restart
    

### 问题总结

1 . Could not open password file.

症状：web浏览器提示服务器端错误，查看httpd日志文件/var/log/httpd/error_log，发现如下错误信息：

    Permission denied:......Could not open password file: /home/gerrit/gerrit/etc/passwords
    

原因：该问题是由于passwords文件的路径上权限设置阻挡了httpd的访问。 解决方法：将/home/gerrit目录及passwords文件的权限设置为755

    $chmod 755 /home/gerrit
    $chmod 755 /home/gerrit/gerrit/etc/passwords
    

2 . Proxy Error

症状：输入用户名和密码登陆后，web浏览器提示代理错误。

    Proxy Error
    
    The proxy server received an invalid response from an upstream server.
    The proxy server could not handle the request GET /login/.
    
    Reason: DNS lookup failure for: 9.181.129.109:8081login
    
    

原因：最后发现是httpd的配置文件中在ProxyPass一行的IP地址后少写了一个/

    ProxyPass / http://9.181.129.109:8081
    --->
    ProxyPass / http://9.181.129.109:8081/
    

### 参考文献

1. [Gerrit官方文档](https://gerrit-documentation.storage.googleapis.com/Documentation/2.11/index.html) 
2. [Gerrit简易安装入门](http://my.oschina.net/zhongl/blog/33017)