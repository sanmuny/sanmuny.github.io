---
title: 'mysql数据库及django用户名启用中文的方法'
date: 2014-12-18 09:49:38
tags: [django,mysql]
published: true
hideInList: false
feature: 
isTop: false
categories: 数据库技术
---

**mysql数据库启用中文**

在mysql的配置文件/etc/my.cnf的\[mysqld\]下加入

    character_set_server=utf8
    

**Django启用中文用户名**

Django默认只能以字母、数字、下划线组成用户名，修改检验用户名的正则表达式可以绕过这一规则：

/usr/lib/python2.7/site-packages/django/contrib/auth/models.py:

    class AbstractUser(AbstractBaseUser, PermissionsMixin):
    ...
    validators.RegexValidator(re.compile('^[\w.@+-]+$'), _('Enter a valid username.'), 'invalid')
    ...
    

把正则表达式从^\[\\w.@+-\]+$ 改为 ^\[\\S.@+-\]+$即可