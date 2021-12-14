---
title: 'Make'
date: 2014-04-24 12:42:03
tags: [make]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

### **一、make的功能：**

make是一个用来维护程序模块关系和生产可执行文件的工具，他可以根据程序修改的情况重新编译链接生成的中间代码或最终的可执行文件。执行make命令需要一个Makefile文件，来定义整个项目的编译规则。makefile定义了模块间的依赖关系，指定文件的编译顺序，以及编译所使用的命令。有了make和Makefile文件，整个项目的源程序可以自动编译，极大的提高了软件开发效率。

### **二、Make的一般使用：**

1、Makefile的基本构成：

Makefile由规则构成，一条规则生成一个或多个目标文件，其格式如下：

    目标文件列表 分隔符 依赖文件列表 [；命令]  //[]中的内容可选
        [命令]
        [命令]

例如：

    main:main.o module1.o module2.o
    	gcc main.o module1.o module2.o -o main
    

第二行是命令必须以Tab键开头，第一行是说明模块间的依赖关系，所以不加Tab，若加Tab,make就认为这是一条命令。以#开头的行为注释行，makefile中若用到#，可用#；同样，$应该用$$。在依赖列表后加上分号后，可直接跟上命令。

命令之间可以插入多个空行，但每行必须有Tab键，如果一行过长，可以在行末输入一个\\，用反斜杠连接的行都被看成一行来处理，反斜杠和新行之间不能有空格。Makefile也可以命名为makefile,若命名为其他文件名，则需要用-f或--file选项来告知make哪一个是makefile文件。

2、Makefile文件的构成：

一个完整的makefile文件由5个部分构成：显式规则、隐含规则、变量、文件指示和注释。

显式规则：一条显式规则指名了目标文件、目标文件的依赖关系、命令。有些规则没有命令，只是说明文件之间的依赖关系。

隐含规则：由make根据目标文件而自动推导出的规则。例如：module2.o:head1.h,实际上等价于module2.o:module2.c head1.h; gcc -c module2.c -o module2.o

变量：类似于c语言中的宏。

文件指示：包括三个部分，一个类似于c语言中的include语句，可以将另一个makefile文件包含进来；二是根据情况指定makefile中的有效部分，就像c语言中的预编译#if一样；三是定义一个多行的命令。

3、makefile的基本语法：

*   |的作用:
    
    foo:foo.c | somelib
        gcc -o foo foo.c somelib
        
当somelib文件的时间戳比foo晚时，不用重新编译foo。
    
*   命令行属性：
    

可在命令前、Tab键后加上如下符号：

    -：执行本命令行如果遇到错误，继续执行而不退出make。
    +：总是执行该命令，即使执行make时使用了-n,-q,-t选项。
    @：执行命令时不在屏幕上输出该命令的内容。
    

*   伪目标：

先看以下代码：

    clean:
    	-rm -rf *.o
    

因为clean后没有依赖文件，所以clean被认为是最新，不会执行rm命令，若要执行rm，需要执行make clean命令。但如果当前目录下已经有一个名叫clean的文件，则make clean也不能使得rm命令执行。这时需要引入伪目标才能搞定。

    .PHONY:clean
    clean:
    	-rm -rf *.o
    

.PHONY的依赖文件为伪目标，作用是伪目标的命令即使在当前目录下存在与伪目标同名的文件时也执行该命令。

*   特殊目标：

.PHONY：伪目标，如上

.IGNORE：对于该目标后的依赖文件，生成时如遇到错误则可跳过错误继续执行，不会中断make。

.SUFFIXES：该目标的依赖被认为是一个后缀列表，在检查后缀规则时使用。

.SILENT：生成该目标文件的依赖文件所执行的命令都不被打印，如果其后无依赖文件，则所有的命令都不会被打印。

.PRECIOUS：该目标的依赖文件将不会被删除。

.INTERMEDIATE：该目标的依赖文件被当作是中间文件对待。

*   多个目标：

一个规则中可以有多个目标，这些目标有相同的依赖文件

*   搜索目录：

通常在一个大的项目中，会把头文件、源文件、库文件放在不同的目录下。当目录发生改变后，只需改变依赖文件的搜索目录。 make定义了一个叫VPATH的变量，在当前目录中搜索不到依赖文件时，便到VPATH定义的目录中去寻找。也可用关键字vpath 定义搜索位置：

    vpath <pattern> <directories>
    vpath :清除所有已被设置好的文件搜索目录。
    

如：vpath %.h ../headers表示在../headers目录下搜索所有.h结尾的头文件。

*   变量：

makefile中通常可定义变量，make在执行时会把变量名出现的地方用变量值代替。

引用变量：$()或者${}

定义变量：

    = ：定义的变量在执行命令时才展开；
    :=：定义的变量立即展开；
    ?=：在此之前没有给该变量赋值才会给该变量赋值
    +=：追加变量值，与原变量值之间用空格隔开
    

预定义变量：

makefile 中预定义了许多变量,在隐含规则中通常会用到这些变量：

    宏名		初始值		说明
    CC			cc			默认使用的编译器
    CFLAGS		-o			编译器使用的选项
    MAKE		make		make命令
    MAKEFLAGS	空 			make命令的选项
    SHELL					默认使用的shell名
    PWD					运行make时的当前路径
    AR			ar			库管理命令
    ARFLAGS   	-ruv		库管理选项
    LIBSUFFIXE	.a			库的后缀
    A			a			库的扩展名
    

自动变量：

它们的值在make运行过程中动态的改变，是隐含规则所必需的变量。

    $@：表示一个规则中的目标文件名。
    $%：当规则中的目标文件是一个静态库文件时，$%就代表静态库的一个成员名。如果目标不是静态库文件，则该变量
        值为空。
    $<：规则中的第一个依赖文件名。
    $>：当规则是一个静态库文件时，该变量表示静态库名。
    $?：所有比目标文件新的依赖文件列表，以空格分隔。如果目标是静态库文件，该变量代表的是
        库成员。
    $^：规则的所有依赖文件列表，以空格分割。如果目标是静态库文件，代表的是库成员。
    $+：和$^类似，不同的是该变量不除去重复的文件。
    $*：去掉后缀的目标文件名。
    

*   条件语句：
    
    ifeq ($(CC),gcc)
    libs=$(libs_for_gcc)
    else
    libs=$(normal_libs)
    endif
    foo:foo.c
        $(CC) -o foo foo.c $(libs)

此外，还有ifneq,ifdef,ifndef

*   使用库：

建立和维护库时，把库名作为目标文件，需要加入库中的文件作为依赖文件。如：

    mylib.a:mylib.a(file1.o) mylib.a(file2.o)或
    mylib.a:mylib.a(file1.o file2.o)
    

接着输入命令：

    ar -ruv 库名 目标文件名
    

三、make命令的常用选项：

    -C dir或--directory=DIR：在读取Makefile文件之前，先切换到dir目录下，即把dir目录作为当前目录。
    -d：输出所有的调试信息。
    -e或--environment-overrides：不允许makefile对系统环境变量进行重新赋值。
    -f filename或者--file=FILE或者--makefile=FILE：使用指定文件作为makefile文件。
    -i或者--ignore-errors：忽略执行make时产生的错误，不退出make。
    -h或者--help：打印出帮助信息。
    -n或者--just-print：只打印出要执行的命令，但不执行命令。
    -o filename或者--old-file=FILE：不重建filename及依赖于filename的文件。
    -p：执行命令之前，打印出make读取的makefile的所有数据，同时打印出make的版本信息。如果只打印信息而
    	不执行命令，可使用make -qp ，查看make执行前的隐含规则和预定义变量，使用make -p-f /dev/null。
    -q：不执行任何命令，返回0表示没有重建目标，返回1表示存在重建目标，返回2表示有错误发生。
    -r：忽略隐含规则。
    -R：取消预定义变量。同时打开-r选项。
    -s：执行但不显示所执行的命令。
    -t：把所有目标文件的最后修改时间设置为当前系统时间。
    -v：打印make版本信息。