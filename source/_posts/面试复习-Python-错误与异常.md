---
title: 面试复习-Python-错误与异常
tags:
  - python
categories: 计算机基础
published: true
hideInList: false
isTop: false
cover: /images/python.png
date: 2024-08-30 19:26:42
feature:
---

### Python中常见的异常

``` python
>>> for
  File "<stdin>", line 1
    for
       ^
SyntaxError: invalid syntax
>>> name
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'name' is not defined
>>> 5/0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
>>> f[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: '_io.TextIOWrapper' object is not subscriptable
>>> l = [1]
>>> l[2]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
>>> d = {'name': 'Sen'}
>>> d.name
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'dict' object has no attribute 'name'
>>> d['name']
'Sen'
>>> f = open("exec.txt")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
FileNotFoundError: [Errno 2] No such file or directory: 'exec.txt'
```

### Python的异常处理

``` python
>>> try:
...     f = open("ex", 'r')
... except IOError as e:
...     print(e)
...
[Errno 2] No such file or directory: 'ex'
```

``` python
# 捕获所有异常
>>> try:
...     f = open("ex", 'r')
... except IOError as e:
...     print(e)
...     raise
... except Exception as e:
...     print(e)
...
[Errno 2] No such file or directory: 'ex'
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
FileNotFoundError: [Errno 2] No such file or directory: 'ex'
```

``` python
# 忽略所有异常
>>> try:
...     f = open("ex", 'r')
... except Exception:
...     pass
```

``` python
>>> try:
...     f = open('a.txt', 'r')
... except IOError:
...     print('file not found')
... else:
...     print('file opened')
... finally:
...     print('file handled')
...
file opened
file handled
```

### 上下文管理协议

``` python
>>> with open('a.txt', 'r') as f:
...     f.readlines()
...
['foo\n', 'bar\n']
```