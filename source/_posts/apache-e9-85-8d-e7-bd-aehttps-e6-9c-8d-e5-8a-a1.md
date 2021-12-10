---
title: 'apache配置https服务'
date: 2015-02-16 11:28:31
tags: [apache,ssl]
published: true
hideInList: false
feature: 
isTop: false
---

1、创建自己签名的证书

    #创建CA签名的证书,需要用到openssl
    yum install openssl    
    #创建key   
    openssl genrsa -des3 -out server.key 1024     
    #创建csr(证书签发请求) 
    openssl req -new -key server.key -out server.csr      
    #生成自己签名的证书
    openssl x509 -req  -in server.csr  -signkey server.key -out server.crt       
    #安装证书
    cp  server.crt /etc/ssl/certs
    cp  server.key /etc/ssl/private
    

2、编辑ssl配置文件

    vim /etc/httpd/conf.d/ssl.conf
    SSLEngine on
    SSLCertificateFile    /etc/ssl/certs/server.crt
    SSLCertificateKeyFile /etc/ssl/private/server.key
    

3、重启apache服务

    #systemctl restart httpd