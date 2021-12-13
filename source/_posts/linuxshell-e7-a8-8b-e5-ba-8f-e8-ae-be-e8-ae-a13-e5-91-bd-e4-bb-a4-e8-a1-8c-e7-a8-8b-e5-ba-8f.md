---
title: 'Linux shell 程序设计3——命令行程序'
date: 2014-04-24 10:30:27
tags: [shell]
published: true
hideInList: false
feature: 
isTop: false
categories: Linux
---

1、date：显示、设置系统的日期和时间。

    $date
    2011年 01月 30日 星期日 14:43:35 CST
    $date 012309232011
    执行结果：设置主机的时间日期为：2011/01/23 09：23
    
    $date  +'%d %H %M'
    

以一定的格式显示时间或日期信息，常见有如下的格式：

    %d   ：显示日
    $date +'%d'
    30
    %D  ：显示日期
    $date +'%D'
    01/30/11
    %e  ：不足二位数的部分不用0补而是用空格补
    %m ：显示月份
    %Y  ：显示公元年
    $date +'%Y'
    2011
    %m  ：显示月
    %M  ：显示分钟
    %S   ：显示秒
    %s   ：显示自1970年1月1日 00：00：00开始到现在的秒数
    $date +'%s'
    1296371799
    $date -R ：产生与RFC-2822兼容的时间字符串
    $date -R
    Sun, 30 Jan 2011 15:20:17 +0800
    

2、cat：连接文件并显示

    cat file1
    显示file1文件的内容
    cat file1 file2
    将file1 和 file2并将结果显示
    cat file1 file2>result.txt
    将file1和file2连接并将结果重定向到result.txt
    

3、wc：计算文件内含的总字数或行数

    wc -l file  ：显示文件file的行数
    wc -c file ：显示文件file的字符
    wc -w file ：显示文件file的单词个数
    

4、find ：在分层目录中寻找文件。

find 路径 样式 操作

例如：

    find /  -name  *.txt -print
    

/为路径，-name *.txt是样式 ，-print是操作， 表示将结果打印到标准输出。

    find / -name *.txt -exec rm-f {} \;
    

-exec的操作表示找到所有的.txt文件之后 执行 rm -f命令，{}表示找到的所有结果，;是-exec的终止符，由于;是shell的特殊符号，所有要用\\将其转义。

    find /etc -cnewer /etc/passwd
    

在/etc 目录中寻找比/etc/passwd文件异动时间新的文件

    find /etc -type d -print
    

将/etc目录中所有类型为d（也就是目录）的文件打印出来

其他的类型还有：

b：块设备文件 c：字符设备文件 d：目录 p：管道 f：一般文件 l：链接文件 s：socket

5、basename：取得路径名称中最后的文件名部分

如：

    basename /etc/configure.sh
    

执行结果：configure.sh

6、dirname：取得路径中目录部分

如：

    dirname /etc/configure.sh
    

执行结果：/etc

7、sort：按ascii码的行首字母对文件的行做排序

    sort file1  ：按ascii码值增大的顺序
    sort -r file1：按ascii码值减少的顺序
    sort -n file1：按字符串比较
    sort -k 2 file1：按字符串比较每行的第二个字段
    sort -nk 2 file1：按数值比较每行的第二个字段
    sort -nr +2 -t: /etc/passwd ：+2表示跳过前两个字段，-t: 表示该:为字段分隔符
    

8、uniq：删除重复行，若重复行没相邻，则无作用

uniq -d： 挑出重复行 uniq -c： 计算每一行的重复次数

如：

编辑文件q，文件内容如下：

    baaaaaaaaaa
    baaaaaaaaaa
    baaaaaaaaaa
    baaaaaaaaaa
    aaaaaaaaaaa
    aaaaaaaaaaa
    dddddddddd
    dddddddddd
    dddddddddd
    dddddddddd
    dddddddddd
    dddddddddd
    

执行命令uniq -d q结果为：

    baaaaaaaaaa
    aaaaaaaaaaa
    dddddddddd
    

执行命令uniq -c q结果为：

    4 baaaaaaaaaa
    2 aaaaaaaaaaa
    6 dddddddddd
    

该命令和sort命令一样，都不改变原文件内容，若要保存结果，可通过重定向和管道。

9、cut ：从文件中抽出某一部分

如：

    cut -c2 q ：从文件q中抽出每一行的第2个字符
    cut -c2-10 q：从文件中抽出每一行的第2到第10个字符
    cut -c2- q：从q中抽出每一行第2个及其以后的字符
    cut -d: -f3,4 passwd：从文件passwd中抽出每一行的第3个和第4个字段，-d：表明:为分割符
    

10、paste：把两个文件按行合并，默认以Tab分割

    paste -d'#' file1 file2：以#分割
    paste -s file：file的每一行和自己的每一行合并
    

11、tr：转换和删除字符。

如：

    $tr k K < file1
    

将file1中所有的k换成K

    $tr -d k <file1
    

将file1中所有的k删除

    $tr '[A-Z]' '[a-z]' <file1
    

将file1中所有的大写字母换成小写字母

    $cut -d: -f1-6 /etc/passwd |tr ：‘+’
    

将passwd文件中前六个字段中的分隔符用+代替

12、grep：显示符合样式的行

    grep A * ：将含有A这个字符的文件及行打印出来
    grep -i A * ：-i 表示不区分大小写，A或a都行
    grep -v A file ：将file中所有不包含A的行打印出来
    grep -l teacher *：只显示含有teacher的文件的文件名而不显示具体的行
    grep -n teacher *：显示文件名和行号
    grep -q teacher filename ：若filename文件中含有teacher关键字则返回0，否则返回非0
    grep -A 200 -e 'wadfadfdf' filename ：表示在filename 中查找wadfadfdf行并显示其后的200行 
    

13、 tee：从标准输入读取数据，显示在标准输出上，并将内容写在指定的文件中。

    $tee filename
    

若filename已经存在，则清空其内容，否则新建一个文件。按ctrl+D组合键，输入的数据就存储在filename中。

    $tee -a filename 以追加的方式写入文件
    

14、diff：比较两个文件之间的差异

15、comm：以列和列的方式比较两个已排序好的文件

如：

file1 文件的内容如下：

    1 2 3
    6 5 4
    9 8 7
    a b c 
    file2：
    2 4 5
    6 5 4
    8 0 9 
    x y z
    

执行comm file1 file2后的结果：

    1 2 3
    2 4 5
    6 5 4
    8 0 9   
    9 8 7
    a b c
    x y z
    

第1列为file1与file2不同的内容，第2列为file2与file1不同的内容，第3列为file1和file2相同的内容。

16、xargs：安排标准输入给要执行命令的参数

如：

    $find . -name *.txt | xargs -n 2 diff
    

将找到的.txt文件以两个一组的方式交给diff进行比较

17、按以下格式可执行多个命令：

    A、命令1；命令2；命令3...	 执行一组命令，不能保证每个命令都成功执行
    B、命令1&&命令2&&命令3... 	  依次执行命令1、命令2...直到执行失败
    C、命令1||命令2||命令3...       依次执行命令1、命令2...直到执行成功
    D、（命令1；命令2；...）  开启一个子shell去执行该组命令 
    E、{  命令1；命令2；...  } 在现行的shell中执行该组命令，{右和}左有至少一个空格
    

18、script:：记录命令执行内容。

    $script com.log
    $ls 
    $exit
    

ls命令的执行结果会被被保存在com.log中