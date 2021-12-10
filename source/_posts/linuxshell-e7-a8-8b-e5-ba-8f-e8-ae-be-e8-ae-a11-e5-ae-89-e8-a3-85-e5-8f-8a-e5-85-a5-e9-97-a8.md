---
title: 'Linux shell 程序设计1——安装及入门'
date: 2014-04-24 09:48:58
tags: [shell]
published: true
hideInList: false
feature: 
isTop: false
---

1、什么是shell？

shell是linux内核的“壳”，是用户和内核的桥梁。它类似于windows下的命令提示符，将用户输入的命令解释给内核执行，并返回给用户结果。与windows命令提示符不同的是，shell还是一种脚本语言，可以按一定的流程将命令组合在一起使用，方便了用户。

2、shell的安装：

在ftp.gnu.org/gnu/bash可下载到bash的源码包：

bash-4.1.tar.gz

用 tar xzvf bash-4.1.tar.gz 解压，生成bash-4.1目录

使用cd 命令进入该目录，在该目录下执行./configure命令生成配置文件，再使用make命令编译，使用make install命令安装。

在/etc/shells文件中列出的shell才是合法的shell，所以要使用安装的shell必须把它加到该文件中。加入之后就可以通过chsh命令来切换shell。

3、shell中的特殊符号：

[http://blogold.chinaunix.net/u2/75431/showart_1110962.html](http://blogold.chinaunix.net/u2/75431/showart_1110962.html)

4、shell的程序结构：

以#!开头，指名要解释、执行该脚本的shell，如：

    #! /bin/bash
    

其余以#开头的行为注释。除此之外，一个shell脚本还包括变量设定、内置命令、函数、以及流程控制语句。

下面是一个简单的shell脚本：
```
      1 #/bin/bash
      2 #This is a test shell script
      3 #It's function is show how to use the function of a shell script
       
      4 /*定义了一个函数，其中$1,$2,$3是传递给该函数的参数*/
      5 function show(){
      6     echo "Today is $1.Your name is $2,and your ip address is $3."       
      7 }
      
      8 /*定义了三个变量*/
      9 name="$1"
     10 date=`date +%F`
     11 ip="222.24.19.12"
     
     12 /*$#为执行shell脚本时传递给该脚本的参数的个数，脚本名不计*/ 
     13 if [ $# != 1 ]; then
     14     echo "input error!"
     15     exit
     16 fi
    
     17 /*调用上面定义的函数*/
     18 show "$date" "$name" "$ip"
     19 
     20 sleep 5
     21 echo     //输出一个空行
     22 echo "exited!"
```  

5、shell脚本排错：

在执行shell脚本之前，需要修改该脚本的权限：

    chmod 755  脚本名
    

可以用两种方式执行该脚本：

*   ./脚本名 参数 或 bash 脚本名 参数 以这种方式执行一个shell脚本，bash会创建一个子shell来执行，所用的环境是子shell的执行环境，当执行结束后又会回到父shell的执行环境。
    
*   . /脚本名 参数 或 source 脚本名 参数 以这种方式执行的shell脚本，bash不会创建子shell，而是在自己的环境中执行，执行完成后，若脚本中有修改环境的地方，则bash的环境就会改变。
    

shell脚本由于是脚本程序，无需编译，所以排错只能依靠阅读源码排错或者是使用 bash -x 脚本名 参数 的执行方式追踪脚本的执行过程

6、shell脚本执行原理：

用户在登录之后，就会进入一个shell环境，称之为父shell，其他脚本执行时称之为子shell。每个用户都有一个默认的登录shell，保存在/etc/passwd文件中。用户可执行chsh修改默认的登录shell。子shell会继承父shell的环境变量。子shell也可以使用 bash命令再创建一个子shell，使用exit 退出一个shell。使用echo $SHLVL可以查看位于第几层shell中。

7、bash的启动配置文件：

用户登录时，login shell 会读取/etc/profile并执行，接着检查用户家目录中是否有.bash\_profile，有则执行，然后检查是否有.bash\_login ，有则执行，最后检查.profile，有则执行。注销的时候，bash会检查用户家目录中是否有.bash\_logout，有则执行。 在执行一个新的shell时，若执行的是交互式shell，或者叫做命令，bash会检查并执行/etc/bash.bashrc以及家目录中的.bashrc。若执行的脚本，则检查BASH\_ENV变量，并执行该变量所指向的文件。