---
title: '迁移宁强在线步骤'
date: 2015-11-18 16:26:16
tags: [网站迁移]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
---

### 配置ssh

    #mkdir /root/.ssh && cp id_rsa* /root/.ssh/
    

### 下载配置脚本

    #git clone git@git.oschina.net:wangsen/auto_conf_fc.git
    

### 安装基础软件

    #cd auto_conf_fc && bash -x ./conf.sh
    

### 安装web软件

    #yum install net-tools httpd python-django                                   
    #yum install python python-pip  python-devel  python-wsgi  mod_wsgi   mariadb-server python-mysql mariadb-devel.x86_64 MySQL-python python-html5lib
    

### 克隆网站代码

    #cd /var/www/html && git clone git@git.oschina.net:wangsen/TownInfo-.git
    #mvTownInfo- nqys
    #cd nqys && cp httpd.conf /etc/httpd/conf/httpd.conf
    

### 安装pip

    #cd ~/auto_conf_fc/nqzx/ && tar xzvf pip-1.5.6.tar.gz && cd pip-1.5.6 && python get-pip.py
    

### 安装Python包

    # cds && pip install -r ../requirements.txt
    

### 启动数据库

    #vim /etc/my.cnf
    # ## character_set_server=utf8
    # systemctl enable mariadb
    # systemctl restart mariadb
    # mysqladmin -u root password
    # mysqladmin -u root -p create nqysdb
    # mysql -u root -p
    ##GRANT ALL PRIVILEGES ON nqysdb.* TO 'django_user'@'localhost' IDENTIFIED BY 'passw0rd'; 
    

### 替换django和registration

    # cd /usr/lib/python2.7/site-packages && rm -rf django
    # git clone git@git.oschina.net:wangsen/django.git
    # git clone git@git.oschina.net:wangsen/registration.git
    

### 解决头像上传问题

    # cd ~/auto_conf_fc/nqzx && tar xzvf Imaging-1.1.7.tar.gz
    # yum install zlib zlib-devel libjpeg libjpeg-devel freetype freetype-devel
    # cd Imaging-1.1.7 && vim setup.py
    修改setup.py：
    TCL_ROOT = "/usr/lib64/"
    JPEG_ROOT = "/usr/lib64/"
    ZLIB_ROOT = "/usr/lib64/"
    TIFF_ROOT = "/usr/lib64/"
    FREETYPE_ROOT = "/usr/lib64/"
    LCMS_ROOT = "/usr/lib64/"
    # pip uninstall pillow PIL
    # python setup.py install
    

### 导入备份数据库

1.  备份数据库

    # mysqldump -u root -p nqysdb > nqzx.db
    

1.  导入数据库

    # mysql -u root -p nqysdb < nqzx.db
    

### 启动web服务

    # cds && ../manage.py collectstatic
    # ../manage.py syncdb
    # service httpd restart