---
title: 'Linux shell 程序设计5——shell中一些特殊符号的用法总结'
date: 2014-04-24 10:54:04
tags: [shell]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

1、{} 大括号：

    eg: ls my_{finger,toe}s

这条命令相当于如下两个命令的组合：

    ls my_fingers ; ls my_toes
    eg: mkdir {userA,userB,userC}-{home,bin,data}

我们得到 userA-home, userA-bin, userA-data, userB-home, userB-bin, userB-data, userC-home, userC-bin, userC-data，这几个目录

大括号也可用于语句块的构造，命令之间可用回车隔开

2、[] 中括号：允许匹配方括号中任何一个单个字符

eg: ls /[eh][to][cm]*

相当于执行 ls /etc 和 ls /home。

常出现在流程控制中，其作用是括住判断式。注意：\[、\] 与表达式之间有空格。

eg:

    if [ "$?" != 0 ]
    then echo "Executes error"
    fi
    

3、`command` 反引号：反引号中的指令将会被执行

    eg: fdv=`date +%F`

在倒引号内的 date +%F 会被视为指令，执行的结果会带入 fdv 变量中

4、'string' 单引号 和 "string" 双引号：如果想在定义的变量中加入空格，就必须使用单引号或双引号，单、双引号的区别在于双引号转义特殊字符，而单引号不转义特殊字符。

    eg: heyyou=home
        echo '$heyyou'
        # We get $heyyou
    eg: heyyou=home
        echo "$heyyou"
        # We get home


5、$#：它的作用是告诉你引用变量的总数量是多少

6、$$：它的作用是告诉你shell脚本的进程号

7、$1、$2、$3……${10}、${11}、${12}…… ：表示脚本的各个参数

8、$@:列出所有的参数，各参数用空格隔开

9、AND列表 statement1 && statement2 && statement3 && ……:只有在前面所有的命令都执行成功的情况下才执行后一条命令

10、OR列表 statement1 || statement2 || statement3 || ……:允许执行一系列命令直到有一条命令成功为止，其后所有命令将不再被执行。

11、［ condition \] && command for true || command for false:当条件为真时，执行command for true ,当条件为假时，执行command for false

12、: 冒号：内建指令，但返回值为0

    eg: :
        echo $?
        #We get 0
    while:实现一个无限循环
    

13、; 分号:在 shell 中，担任"连续指令"功能的符号就是"分号"

    eg:cd ~/backup ; mkdir startup ; cp ~/.* startup/.
    

14、~:代表使用者的 home 目录

15、# 井号：表示符号后面的是注解文字，不会被执行

16、\ 倒斜线：放在指令前，有取消 aliases 的作用；放在特殊符号前，则该特殊符号的作用消失；放在指令的最末端，表示指令连接下一行

17、! 惊叹号：通常它代表反逻辑的作用，譬如条件侦测中，用 != 来代表"不等于"

18、* 星号：在文件名扩展上，她用来代表任何字元

19、** 次方运算：两个星号在运算时代表 "次方" 的意思

    eg:let "sus=2**3"
        echo "sus = $sus"
        # sus = 8