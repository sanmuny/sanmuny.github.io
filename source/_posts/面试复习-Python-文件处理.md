---
title: 面试复习-Python-文件处理
tags:
  - python
categories: 计算机基础
published: true
hideInList: false
isTop: false
cover: /images/python.png
date: 2024-08-29 19:26:42
feature:
---

### open函数

在 Python 中，open()函数用于打开文件，并返回一个文件对象，通过这个文件对象可以对文件进行各种操作。

``` python
f = open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)
f.close()   
```

* file：要打开的文件路径，可以是相对路径或绝对路径。
* mode（可选）：打开文件的模式，默认为'r'（只读模式）。常见的模式有：
    - 'r'：只读模式，文件必须存在。
    - 'w'：写入模式，如果文件存在则清空内容，如果文件不存在则创建新文件。
    - 'a'：追加模式，在文件末尾追加内容，如果文件不存在则创建新文件。
    - 'b'：二进制模式，可以与其他模式结合使用，如'rb'（二进制只读模式）、'wb'（二进制写入模式）。。
    - 't'：文本模式（默认），可以与其他模式结合使用，如'rt'（文本只读模式）、'wt'（文本写入模式）。
* buffering（可选）：设置缓冲策略。默认为 - 1，表示使用系统默认的缓冲策略。
* encoding（可选）：指定文件的编码方式，仅在文本模式下使用。例如'utf-8'。
* errors（可选）：指定编码错误处理方式，仅在文本模式下使用。
* newline（可选）：控制换行符的处理方式，仅在文本模式下使用。
* closefd（可选）：如果为True（默认值），则文件描述符会在文件关闭时关闭；如果为False，则文件描述符在文件关闭时不会关闭，这在文件描述符来自于其他地方时很有用。
* opener（可选）：一个自定义的打开文件的函数。

### 文件对象的常用属性与方法

``` python
>>> f = open("body.json", "r+")
>>> f.read()
'{\n  "type": "markdown",\n  "text": "Hello <@W7RR1LJ58>"\n}\n'
>>> f.fileno()
3
>>> f.isatty()

>>> f.isatty()
False
>>> f.readline()
''
>>> f.readline()
''
>>> f.seek(0, 0)
0
>>> f.readline()
'{\n'
>>> f.readline()
'  "type": "markdown",\n'
>>> f.readlines()
['  "text": "Hello <@W7RR1LJ58>"\n', '}\n']
>>> f.tell()
57
>>> f.seek(3, 0)
3
>>> f.read()
' "type": "markdown",\n  "text": "Hello <@W7RR1LJ58>"\n}\n'
>>> f.tell()
57
>>> f.seek(0, 0)
0
>>> f.write("abcdefg")
7
>>> f.tell()
7
>>> f.seek(0,0)
0
>>> f.read(3)
'abc'
>>> f.tell()
3
>>> f.truncate()
57
>>> f.seek(0,0)
0
>>> f.read()
'abcdefgpe": "markdown",\n  "text": "Hello <@W7RR1LJ58>"\n}\n'
>>> f.closed
False
>>> f.encoding
'UTF-8'
>>> f.name
'body.json'
>>> f.mode
'r+'
>>> f.newlines
'\n'
```

### `os`模块常用的文件系统操作函数

```python
>>> f = open('test', 'w')
>>> f.write('foo\n')
4
>>> f.write('bar\n')
4
>>> f.close()
>>> cwd=os.getcwd()
>>> os.listdir(cwd)
['.envrc', 'test', '.whitesource', '.gitignore', 'ai', 'vpc-bss-onboarding', 'fabric_test', '.git', '.travis.yml']
>>> os.rename('test', 'a.txt')
>>> os.path.join(cwd, f.name)
'/Users/senwang/tmp/test'
```
