---
title: 'Python中的参数传递与解析'
date: 2017-02-09 12:17:34
tags: [python]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

Python传递命令行参数
=============

Python的命令行参数传递和C语言类似，都会把命令行参数保存到argv的变量中。对于python而言，argv是sys模块中定义的一个list。与C语言不同的是，python中并没有定义argc，要获得参数的个数，需要使用len(sys.argv)

当用户使用'python -c "command" '来运行一条python语句时，argv中保存的是\['-c'\]及"command"后面的参数，例如：

    $ python -c 'import sys
    print sys.argv' hello world
    ['-c', 'hello', 'world']

当用户使用'python -m "module" '来运行一个模块时，argv中保存的是模块名及"module"后面的参数，例如：

    $ python -m 'show_args' hello world
    ['/home/kelvin/tmp/show_args.py', 'hello', 'world']

当运行python脚本时，argv中保存的是脚本名及其后面的参数：

    $ python show_args.py hello world
    ['show_args.py', 'hello', 'world']
    length of argv: 3
    
    $ cat show_args.py 
    #!/bin/env python
    
    import sys
    
    print sys.argv
    print "length of argv: " + str(len(sys.argv))
    
    

使用标准库getopt解析选项和参数
==================

getopt模块和C语言中的getopt函数有着一样的API，熟悉C语言的同学可快速上手。

    # -*- coding: utf-8 -*-
    #!/bin/env python
    
    import getopt
    import sys
    
    
    def usage():
        print("Usage:")
        print(sys.argv[0] + ' -i input_file -o output_file')
    
    
    def main():
        try:
            opts, args = getopt.getopt(sys.argv[1:], "ho:i:", ["help", "output=", "input="])
        except getopt.GetoptError as err:
            # print help information and exit:
            print("[" + err.opt + "]" + err.msg)
            usage()
            sys.exit(2)
        output = None
        input = None
        for o, a in opts:
            if o in ("-i", "--input"):
                input = a
            elif o in ("-h", "--help"):
                usage()
                sys.exit()
            elif o in ("-o", "--output"):
                output = a
            else:
                assert False, "unhandled option"
        print("Input file: %s" % (input))
        print("Output file: %s" % (output))
    
    if __name__ == "__main__":
        main()

getopt需要传入3个参数：

1.  需要解析的字符串，即sys.argv\[1:\]

2\. 短选项集合。其中跟冒号的短选项需要后接参数，如'o:'表示'-o'选项需要接参数。

3\. 长选项列表。其中跟等号的长选项需要后接参数。

getopt返回一个元组，元组包括两个列表opts和args。opts的元素是一个元组，保存了解析好的选项和参数对。args保存了除去所有选项和选项的参数之外，剩下的所有参数。

如果解析出错则会抛出GetoptError异常，该异常有一个参数err。err.opt是出错时正在解析的选项，err.msg是错误消息。

出错的情况包括：

1\. 选项没有在传入参数中的短选项或者长选项列表定义。

2\. 需要带参数的选项没有跟参数。

3\. 不需要带参数的长选项带了参数。

4\. 其他。

    kelvin@kvm:~/tmp$ python parse_args.py --help=out
    [help]option --help must not have an argument
    Usage:
    parse_args.py -i input_file -o output_file
    kelvin@kvm:~/tmp$ python parse_args.py -i input_file.txt 
    Input file: input_file.txt
    Output file: None
    kelvin@kvm:~/tmp$ python parse_args.py -i input_file.txt --output="hello_world.txt"
    Input file: input_file.txt
    Output file: hello_world.txt
    kelvin@kvm:~/tmp$ python parse_args.py -i input_file.txt -o "output.txt"
    Input file: input_file.txt
    Output file: output.txt

使用标准库argparse来解析选项和参数
=====================

argparse模块功能更加强大，例如可以自动生成help文档等，使用起来也更加简便，只需要三个步骤即可。第一步，创建一个ArgumentParser对象：

    parser = argparse.ArgumentParser(description='Description of the program')

第二步，添加需要解析的选线和参数：

    parser.add_argument('integers', metavar='N', type=int, nargs='+',
                        help='an integer for the accumulator')
    parser.add_argument('--sum', dest='accumulate', action='store_const',
                        const=sum, default=max,
                        help='sum the integers (default: find the max)')

第三部，开始解析：

    args = parser.parse_args()

ArgumentParser
--------------

     class argparse.ArgumentParser(prog=None, usage=None, 
    description=None, epilog=None, parents=[], 
    formatter_class=argparse.HelpFormatter, prefix_chars='-',
     fromfile_prefix_chars=None, argument_default=None, 
    conflict_handler='error', add_help=True, allow_abbrev=True)

常用的参数解释如下：

prog: 指定程序的名字，默认为sys.argv\[0\].

usage: 描述程序该如何使用的字符串，默认会根据添加的参数和选项自动生成

description: 描述程序的功能，默认为空

epilog: epilog指定的字符串将会显示在帮助文档的最后

parents: 一个 ArgumentParser对象的列表，这些对象的选项和参数也会被继承

add_help: 添加-h/--help选项，默认为True

allow_abbrev: 允许长选项的缩写，默认为True

add_argument
------------

     ArgumentParser.add_argument(name or flags...[, action]
    [, nargs][, const][, default][, type][, choices][, required]
    [, help][, metavar][, dest])

**name**将会作为解析后返回的对象args的属性，存储参数的值，flags定义指定的选项，flag的名字也会作为解析后返回的对象的属性，存储该选项的参数。例如：

    parser.add_argument('-f', '--foo')
    parser.add_argument('arg0')
    args = parser.parse_args()
    print(args.foo)
    print(args.arg0)
    

执行结果如下：

    $./arg_parse.py --foo hello world
    hello
    world
    

**nargs**指定选项参数或者参数本身的个数。例如：

    parser.add_argument('-f', '--foo', nargs=2)
    args = parser.parse_args()
    print(args.foo)

执行结果如下：

    $./arg_parse.py --foo hello world
    ['hello', 'world']

argparse会将--foo选项后面的两个参数都作为--foo的参数处理。

**action**指定argparse如何处理该选项的参数，共有8个值可选。

1.  'store': 默认值，表示存储参数，如上面例子中的args.foo存储hello world.
2.  'store_const': 存储常量，常量的值位于const参数中。如：
    
        $ cat arg_parse.py 
        #!/usr/bin/env python
        
        import argparse
        
        parser = argparse.ArgumentParser()
        parser.add_argument('-f', '--foo', action='store_const', const=1024)
        args = parser.parse_args()
        print(args.foo)
        
        $ ./arg_parse.py -f
        1024
        
    
3.  'store\_true'或者'store\_false'：和'store_const'类似，存储的值为True或者False
    
4.  'append'：连个同样的选项的参数会被放到同一个list里面，例如：  
     
    
        $ cat arg_parse.py 
        #!/usr/bin/env python
        
        import argparse
        
        parser = argparse.ArgumentParser()
        parser.add_argument('-f', '--foo', action='append')
        args = parser.parse_args()
        print(args.foo)
        
        $ ./arg_parse.py --foo 1 --foo 2 -f 3
        ['1', '2', '3']
    
5.  'append_const': 可将多个常量存放到一个list中，选项出现几次，list中的常量就出现几次，例如：  
     
    
        $ cat arg_parse.py 
        #!/usr/bin/env python
        
        import argparse
        
        parser = argparse.ArgumentParser()
        parser.add_argument('-f', '--foo', action='append_const', const="1024")
        args = parser.parse_args()
        print(args.foo)
        
        $ ./arg_parse.py --foo --foo -f
        ['1024', '1024', '1024']
    
6.  'count':  存储选项出现的次数。例如：  
     
    
        $ cat arg_parse.py 
        #!/usr/bin/env python
        
        import argparse
        
        parser = argparse.ArgumentParser()
        parser.add_argument('-v', '--verbose', action='count')
        args = parser.parse_args()
        print(args.verbose)
        
        $ ./arg_parse.py -vvv
        3
        
        $ ./arg_parse.py -v --verbose -v
        3
    
7.  'help': 当出现这个选项时，程序打印help文档然后退出。
    
8.  'version': 当出现这个选项时，程序打印版本信息然后退出，版本信息可通过version定义，例如：  
     
    
        $cat arg_parse.py
        #!/usr/bin/env python
        
        import argparse
        
        parser = argparse.ArgumentParser(prog="wchat")
        parser.add_argument('-v', '--version', action='version', version='%(prog)s 3.8.5')
        args = parser.parse_args()
        
        $ ./arg_parse.py --version
        wchat 3.8.5
    

**required**指定该参数或者选项是必须提供的，否则会报错退出。

**type**指定参数的类型，可以是任何python内建的数据类型如int等，也可以是自定义的类型转换函数的函数名。例如：

    $ cat ./arg_parse.py 
    #!/usr/bin/env python
    
    import argparse
    
    parser = argparse.ArgumentParser()
    parser.add_argument('-f', '--foo', type=int)
    args = parser.parse_args()
    print(args.foo)
    
    $ ./arg_parse.py -f 12
    12
    
    $ ./arg_parse.py -f '12'
    12
    
    $ ./arg_parse.py -f hello
    usage: arg_parse.py [-h] [-f FOO]
    arg_parse.py: error: argument -f/--foo: invalid int value: 'hello'

**choices**指定一组参数，选项的参数必须从这组参数中来选取。例如：

    $ cat arg_parse.py 
    #!/usr/bin/env python
    
    import argparse
    
    parser = argparse.ArgumentParser()
    parser.add_argument('-f', '--foo', type=int, choices=range(1, 4))
    args = parser.parse_args()
    print(args.foo)
    
    $ ./arg_parse.py -f3
    3
    
    $ ./arg_parse.py -f19
    usage: arg_parse.py [-h] [-f {1,2,3}]
    arg_parse.py: error: argument -f/--foo: invalid choice: 19 (choose from 1, 2, 3)

**help**指定参数或者选项的帮助信息，会出现在help文档里。

metavar可以改变帮助文档中选项的参数占位字符串，例如，--foo默认的占位字符串为FOO，可以通过metavar改为foo_arg：

    $ cat arg_parse.py 
    #!/usr/bin/env python
    
    import argparse
    
    parser = argparse.ArgumentParser()
    parser.add_argument('-f', '--foo')
    args = parser.parse_args()
    print(args.foo)
    
    $ ./arg_parse.py -h
    usage: arg_parse.py [-h] [-f FOO]
    
    optional arguments:
      -h, --help         show this help message and exit
      -f FOO, --foo FOO
    
    $ cat arg_parse.py 
    #!/usr/bin/env python
    
    import argparse
    
    parser = argparse.ArgumentParser()
    parser.add_argument('-f', '--foo', metavar='foo_arg')
    args = parser.parse_args()
    print(args.foo)
    
    $ ./arg_parse.py -h
    usage: arg_parse.py [-h] [-f foo_arg]
    
    optional arguments:
      -h, --help            show this help message and exit
      -f foo_arg, --foo foo_arg

dest可以修改存储参数的属性名，例如：

    $ cat arg_parse.py 
    #!/usr/bin/env python
    
    import argparse
    
    parser = argparse.ArgumentParser()
    parser.add_argument('-f', '--foo', dest='bar')
    args = parser.parse_args()
    print(args.bar)
    
    $ ./arg_parse.py -f hello
    hello
    

小结
==

getopt虽然提供了接近Unix C的用户接口，方便了熟悉Unix C的程序猿/媛们，但argparse模块功能更为强大，使用起来也更为简洁，所以大多数的python项目都采用argparse来解析参数。