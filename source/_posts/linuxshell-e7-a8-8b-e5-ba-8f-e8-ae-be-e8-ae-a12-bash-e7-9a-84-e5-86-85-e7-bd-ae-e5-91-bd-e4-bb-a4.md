---
title: 'Linux shell 程序设计2——bash的内置命令'
date: 2014-04-24 10:12:50
tags: [shell]
published: true
hideInList: false
feature: 
isTop: false
---

常用的内置命令忽略，来看看shell编程中其他一些重要的内置命令：

1、help：显示所有内置命令列表，或显示一个具体命令的用法。

-s： 表示列出命令的语法格式

例子：

help -s help help: help \[-dms\] \[pattern ...\]

2、echo：用来显示一行文字。默认自动换行。

    -n：取消自动换行。
    -e：让字符串中的特殊字符起作用，即使字符串在单引号中。
    

例子： echo hello world 或 echo 'hello world' 或 echo "hello world" 输出结果为：hello world $echo -e "hello \\n world" hello world $ echo -e 'hello \\n world' hello world

3、printf：显示格式字符串，类似于c中的printf函数

格式：printf “格式字符串” 参数

    -v ：不显示到标准输出，而是赋值给-v选项后面的变量
    

例子：

    str= "hello world"
    printf "%s\n" "$str"     执行结果：hello world
    
    printf -v str "hello world" 
    echo $str                      执行结果：hello world
    
    printf "%q" "hello world \n" 执行结果：hello\ world\\n 无换行
    

%q这个选项我想了很久才弄明白它的含义：

将字符串或变量中的转义字符 用 源码格式替换。于是，上面的例子中，空格变成了\\空格，\\变成了\\，而且\\n没有了换行的作用

printf详细用法请参考博客：

[http://bbs.chinaunix.net/thread-845520-1-1.html](http://bbs.chinaunix.net/thread-845520-1-1.html)

4、: 什么也不做，返回0

例子：

    :
    echo $?                    运行结果：0
    

5、. 或 source ：在现行shell中执行shell程序

例子：

编辑脚本文件a_var.sh：

    #!/bin/bash
    a=31
    

保存退出后回到终端，修改a_var.sh的权限并输入命令：

    ./a_var.sh
    

执行，然后在终端执行：

    echo $a
    

输出结果为一个空行，意味着变量a的值为空，我们再以命令.空格a\_var.sh 或source a\_var.sh 执行，然后输入：

    echo $a                     其输出结果为：  31
    

第一种方式执行a_var.sh，bash会创建一个shell去执行，当子shell执行完成后，它的变量a会被系统收回。

6、alias：显示或设定程序别名

例子：

    alias          执行结果：列出所有的别名
    alias ll='ls -al'  
    ll               执行结果：等价于执行了  ls -al
    

7、unalias：取消别名

    alias ll
    

8、exit ：离开脚本或登录shell，可带返回值

exit 1

9、history：显示过去曾经执行过的shell指令，与history命令相关的有三个重要的变量：

HISTFILE ：记录存放历史命令文件的路径，如：

    echo $HISTFILE                    结果为：/home/kelvin/.bash_history
    

HISTFILESIZE：设置历史命令文件命令的最大个数，超过这个个数，序号在前的命令记录就会被删除

HISTSIZE：设置终端中交互式命令的历史记录个数。它和HISTFILESIZE相比的最小值起作用。

10、fc：列出登录主机后最近执行过的命令。一般和选项 -l 配合使用。

例子：

    $fc -l      结果：
    
    363	 cat /etc/profile
    364	 echo $HISTORY
    365	 echo $HISTORYFILE
    366	 echo $HISTFILE
    367	 ehco $HISTFILESIZE
    368	 echo $HISTFILESIZE
    369	 echo $HISTSIZE
    370	 history 
    371	 history
    372	 echo $HISTFILE
    373	 lw
    374	 ls
    375	 fc -l
    376	 fc -l 368
    377	 fc -l echo l
    378	 fc -l
    fc -l 375     列出375行以后的命令                输出结果：
    375	 fc -l
    376	 fc -l 368
    377	 fc -l echo l
    378	 fc -l
    fc -l 375 377 列出375到377之间的命令          输出结果：
    375	 fc -l
    376	 fc -l 368
    377	 fc -l echo l
    fc -l echo  l    列出从 关键字 echo 到 l之间的内容     输出结果：
    372	 echo $HISTFILE
    373	 lw
    374	 ls
    

11、type：对一个命令的类型进行说明(包含命令行程序)。

例子：

    $type ls
    
    ls 已被别名为“ls --color=auto”
    
    $type cp
    
    cp 是 /bin/cp
    
    $type fc
    
    fc 是一个 shell 内部命令
    

12、set：列出所有变量和函数的内容，加入选项可以设置bash的某个属性是否打开

例子：

    $set -o 查看所有属性，或打开某个属性
    
    allexport      	off
    braceexpand    	on
    emacs          	on
    errexit        	off
    errtrace       	off
    functrace      	off
    hashall        	on
    histexpand     	on
    history        	on
    ignoreeof      	off
    interactive-comments	on
    keyword        	off
    monitor        	on
    noclobber      	off
    noexec         	off
    noglob         	off
    nolog          	off
    notify         	off
    nounset        	off
    onecmd         	off
    physical       	off
    pipefail       	off
    posix          	off
    privileged     	off
    verbose        	off
    vi             	off
    xtrace         	off
    
    $set -o notify
    set -o              打开notify属性后显示所有属性状态，输出结果：
    allexport      	off
    braceexpand    	on    
    emacs          	on
    errexit        	off
    errtrace       	off
    unctrace      	off
    hashall        	on
    histexpand     	on
    history        	on
    ignoreeof      	off
    interactive-comments	on
    keyword        	off
    monitor        	on
    noclobber      	off
    noexec         	off
    noglob         	off
    nolog          	off
    notify         	on
    nounset        	off
    onecmd         	off
    physical       	off
    pipef    ail       	off
    posix          	off
    privileged     	off    
    verbose        	off
    vi             	off
    xtrace         	off
    
    set  +o notify 
    set -o         关闭notify属性，并显示所有属性状态：
    allexport      	off
    braceexpand    	on
    emacs          	on
    errexit        	off
    errtrace       	off
    functrace      	off
    hashall        	on
    histexpand     	on
    history        	on
    ignoreeof      	off
    interactive-comments	on
    keyword        	off
    monitor        	on
    noclobber      	off
    noexec         	off
    noglob         	off
    nolog          	off
    notify         	off
    nounset        	off
    onecmd         	off
    physical       	off
    pipefail       	off
    posix          	off
    privileged     	off
    verbose        	off
    vi             	off
    xtrace         	off
    

set -C 或 set -o noclobber ：保护已存在文件，不让重定向覆盖文件内容，只能追加。

例如：

    set -C 
    touch a.c
    echo adfad >  a.c  提示出错：
    bash: a.c：无法覆盖已经存在的文件
    

但当我们追加内容时不会提示出错：

    echo adfasf >> a.c
    

可用set +C 取消

set -u：用于测试变量是否存在

例如：

    : $i
    

echo $? 这儿的返回值应该为1，因为变量i不存在

    i=1
    : $i
    

echo $? 这儿的输出结果应该是0。同样，可以用set +u取消作用

set -v：显示当前shell的每一个执行命令，换句话说，就是把执行的命令打印出来

例如：

    kelvin@kelvin-Founder:~$ set -v
    kelvin@kelvin-Founder:~$ ls
    ls
    a.c  Linux material  project_files  record  shell  software  桌面
    

可用于对shell脚本的排错，该属性可用set +v取消作用

13、shopt：很多方面都和set命令一样，但它增加了很多选项。

    -s：开启选项
    -u：关闭选项
    -o：set -o
    -q：以返回值的形式表示开关状态，非0表示关，0表示开
    

set和shopt 的细节参见：

[http://blogold.chinaunix.net/u3/94271/showart.php?id=2195391](http://blogold.chinaunix.net/u3/94271/showart.php?id=2195391)

14、read：从标准输入读取一行数据

例子：

    #!/bin/bash
    echo "please input your name "
    read your_name                             //如果不输入your_name，读取结果会默认存入变量ERPLY
    echo "your name is :"  $your_name   
    

执行结果：

    please in put your name 
    kelvin
    your name is : kelvin
    

read -p "提示信息" ；所以上述sh脚本也可写成：

    #!/bin/bash
    read -p "please input your name" your_name
    echo "your name is:" $your_name
    

read -a arr：将一行数据存入数组arr

例如：

    read -a arr <<(echo 1 2 343 23) 
    

这样，echo ${arr\[2\]} 的输出结果就是343

read还可以读值给多个变量：

    IFS=':'
    read f1 f2 f3 f4 f5 f6 f7 < /etc/passwd
    

因为passwd中7个字段是由:分割开的，所以令IFS=':'

15、time：打印设置命令执行的real user sys时间，real 表示命令真正运行时间，cpu使用时间由两部分表示： user表示用户态程序执行时间， sys 表示系统调用时间。

例如：

    time ls
    time ls
    adf.sh  a_var.sh  name.sh
    
    real	0m0.004s
    user	0m0.000s
    sys	0m0.000s
    

16、exec：后接命令或程序，执行命令或程序，并取代原来的shell执行环境；执行重定向生效，例如：

exec < file 那么凡是由标准输入读入数据的操作都改为由file读入数据

17、eval：读取变量，并将变量的内容作为命令执行

例如：

    listlog="ls -al /var/log/*.log"
    eval $listlog
    

执行结果：ls -al /var/log/*.log将被执行。