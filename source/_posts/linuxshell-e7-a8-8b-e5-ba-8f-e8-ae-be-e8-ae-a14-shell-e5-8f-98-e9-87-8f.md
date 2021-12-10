---
title: 'Linux shell 程序设计4——shell变量'
date: 2014-04-24 10:41:52
tags: [shell]
published: true
hideInList: false
feature: 
isTop: false
---

1、shell变量没有类型，所有变量都被当作字符串来处理。

2、shell变量的命名和c语言相同。

3、shell变量赋值和c语言略有不同，shell赋值要求等号的两边不能出现空格，而在linux C 中，一般为了增强代码的可读性，等号的两边都加一个空格。如果shell变量的赋值为字符串，而且字符串中含有空格，则必须给该字符串加单引号或双引号。

4、shell变量不同于c语言，无需定义可直接赋值使用。例如：

    #!/bin/bash
    #This is an example to show how to use variables
    
    version="2.6.24"
    name="linux-headers-2.6.24"
    
    echo -e "name:$name\nversion:$version"
    

执行结果：

    name:linux-headers-2.6.24
    version:2.6.24
    

5、shell变量的作用范围是本shell环境。例如：

我们编写如下脚本：

    #!/bin/bash
    #script name: exam.sh
    #This is a example to show the action range of a variable
    
    os_name=linux
    echo $os_name
    

当我们使用./exam.sh执行脚本结果为：

    linux
    

然后我们在命令行中执行:

    echo $os_name 结果为空
    

而如果使用 source exam.sh 执行脚本或者是 .空格exam.sh命令执行脚本后键入echo $os_name 命令，我们会得到：

    linux
    

6、有一种能继承给子shell的变量，称之为环境变量。让一个变量变身为环境变量的方法为：

    export 变量名
    

例如：在终端中我们敲入如下命令：

执行脚本：

    #!/bin/bash
    echo $a
    

我们什么也不能得到。而如果在终端中使用命令：

    export a=linux
    

然后执行上述脚本，我们的到结果：

    linux
    

7、shell内置变量：bash设置了许多内置变量，在进行shell程序设计的时候可能需要用到。详见：

    $?：最后一次执行的命令的返回码
    $$：shell进程自己的PID
    $!：shell进程最近启动的后台进程PID
    $#：shell脚本参数个数，不含脚本名
    $0：脚本文件本身的名字
    $1、$2...：第一个参数、第二个参数...
    $*：代表所有的参数（不含脚本名）组成的字符串
    $@：命令行参数组成的多个字符串，每个参数对应一个
    

8、设置shell变量属性：

readonly：使用readonly命令可以