---
title: 面试复习-Python-数据类型
tags:
  - python
categories: 计算机基础
published: true
hideInList: false
isTop: false
cover: /images/python.png
date: 2024-08-27 19:26:42
feature:
---

Python 是一种高级编程语言，具有丰富的数据类型。了解这些数据类型对于有效地编写 Python 代码至关重要。以下是对 Python 主要数据类型的详细介绍。

Python使用对象模型来存储数据，一个python对象通常包含ID，Type, Value。 

### Python数据类型的分类

标准类型：

- 数字
- 整型
- 长整型
- 浮点型
- 复数
- 布尔型
- 字符串
- 列表 (可变)
- 元组
- 字典 (可变)


其他内置数据类型：

- 类型
- None
- 文件
- 集合
- 函数
- 模块
- 类

布尔值为False的对象： `None, False, 0, 0.0, 0L, 0.0+0.0j, "", [], (), {}` (JS中`[], {}`转换成布尔值后为true)

### 标准类型的操作符

1. 数值比较操作符有： `< > <= >= == != <>`
2. ID比较: `is` 和 `is not`
3. 逻辑运算: `and or not`
4. 用于标准类型的内置函数: `type(o) repr(o) str(o) isinstance(o, O) id(o)`

### 序列类型操作符

字符串、元组和列表为序列类型，序列类型的操作符如下：
``` python
>>> string = "hello world"
>>> string[2]
'l'
>>> string[2:5]
'llo'
>>> string[2:5:2]
'lo'
>>> string*2
'hello worldhello world'
>>> string + string
'hello worldhello world'
>>> "l" in string
True
>>> "l" not in string
False
```

### 操作序列类型的内建函数
``` python
>>> for i, v in enumerate(string):
...     print(i,v)
...
0 h
1 e
2 l
3 l
4 o
5
6 w
7 o
8 r
9 l
10 d
>>> len(string)
11
>>> max(string)
'w'
>>> min(string)
' '
>>> for v in reversed(string):
...     print(v)
...
d
l
r
o
w

o
l
l
e
h
>>> sorted(string)
[' ', 'd', 'e', 'h', 'l', 'l', 'l', 'o', 'o', 'r', 'w']
ValueError: not enough values to unpack (expected 2, got 1)
>>> for v in zip(string):
...     print(v)
...
('h',)
('e',)
('l',)
('l',)
('o',)
(' ',)
('w',)
('o',)
('r',)
('l',)
('d',)
```

### 数字

- 整型： 最大取值取决于机器字长
- 长整型：最大取值取决于虚拟内存的大小
- 强制类型转换：整形->浮点型->复数->长整型
- 操作符：`/ // % **`
- 整型的位运算符：`~ << >> & ^ |`

``` python
# 数字类型的操作函数
>>> bool(5)
True
>>> bool(0)
False
>>> int(5.0)
5
>>> int('01101', 2)
13
>>> float('5.0')
5.0
>>> float(5)
5.0
>>> complex(1, 2)
(1+2j)
>>> complex('1+2j')
(1+2j)

>>> pow(2, 2)
4
>>> pow(2, 3)
8
>>> pow(2, 3, 3)
2
>>> round(5.354, 2)
5.35
>>> round(5.3)
5
>>> round(5.6)
6
>>> round(5.254, 1)
5.3
```

``` python
# 整型的操作函数
>>> hex(255)
'0xff'
>>> oct(255)
'0o377'
>>> chr(46)
'.'
>>> ord('.')
46
```

``` python
>>> 0.3 == 3 * 0.1 #规避浮点数二进制表示除不尽的问题
False
>>> 0.3 - 3 * 0.1 < 0.00001
True
``` 

### 字符串

#### 格式化字符串

在 Python 中有多种方法格式化字符串，以下是一些主要的方式：

* 使用百分号（%）格式化

```python
name = "Alice"
age = 25
print("My name is %s and I am %d years old." % (name, age))
```
其中 %s 表示字符串占位符，%d 表示整数占位符。

* 使用 .format() 方法

位置参数：

```python
print("My name is {} and I am {} years old.".format(name, age))
```

关键字参数：
```python
print("My name is {n} and I am {a} years old.".format(n=name, a=age))
```

索引参数：
```python
print("My name is {0} and I am {1} years old.".format(name, age))
```

* 使用 f-strings（Python 3.6 及以上版本）


```python
print(f"My name is {name} and I am {age} years old.")
```
f-strings 非常直观和简洁，可以在字符串中直接嵌入变量和表达式。
此外，还可以在格式化字符串中指定格式规范，例如：
```python
pi = 3.1415926
print(f"Pi is approximately {pi:.2f}")
```
这里 .2f 表示将 pi 格式化为保留两位小数的浮点数。

#### 字符串对象常用的方法

``` python
>>> string = "hello world!"
>>> string.find("ll", 0, len(string))
2
>>> string.index("iam")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found
>>> string.find("iam")
-1
>>> ":".join(["key", "value"])
'key:value'
>>> " hello ".lstrip()
'hello '
>>> " hello ".rstrip()
' hello'
>>> " hello ".strip()
'hello'
```

### 列表

#### 列表对象的常用方法

``` python
>>> mylist = []
>>> mylist.append("hello")
>>> mylist
['hello']
>>> mylist.extend(["world"])
>>> mylist
['hello', 'world']
>>> mylist.insert(1, True)
>>> mylist
['hello', True, 'world']
>>> mylist.pop()
'world'
>>> mylist
['hello', True]
>>> mylist.reverse()
>>> mylist
[True, 'hello']
>>> mylist.remove(True)
>>> mylist
['hello']
>>> mylist.extend(["world", 1, 0])
>>> mylist
['hello', 'world', 1, 0]
>>> mylist.remove("hello")
>>> mylist.remove("world")
>>> mylist.sort()
>>> mylist
[0, 1]
```

### python对象的浅拷贝与深拷贝

在 Python 中，对象的浅拷贝（shallow copy）和深拷贝（deep copy）是两种不同的复制方式。

#### 浅拷贝

创建一个新的对象，但是新对象中的元素是原对象中元素的引用。这意味着如果原对象中的可变对象（如列表、字典等）发生改变，浅拷贝得到的新对象中的对应元素也会改变。使用方法如下：
* 使用切片操作进行浅拷贝，例如 new_list = old_list[:]。
* 使用 copy 模块的 copy() 函数，例如 import copy，然后 new_list = copy.copy(old_list)。
示例：
```python
>>> mylist = [1, [2, 3], 4]
>>> yourlist = mylist[:]
>>> mylist[1][0] = 3
>>> mylist
[1, [3, 3], 4]
>>> yourlist
[1, [3, 3], 4]
```

#### 深拷贝
创建一个新的对象，并且新对象中的元素与原对象中的元素完全独立，不是引用关系。这意味着无论原对象中的可变对象如何改变，深拷贝得到的新对象都不会受到影响。
使用 `copy` 模块的 `deepcopy()` 函数，例如 `import copy`，然后 `new_list = copy.deepcopy(old_list)`
示例：
``` python
>>> mylist = [1, [2, 3], 4]
>>> import copy
>>> yourlist = copy.deepcopy(mylist)
>>> mylist[1][0] = 3
>>> yourlist
[1, [2, 3], 4]
```

### 字典

* `in, not in` 可以查询key是不是在字典中

#### 字典的常用内建方法

``` python
>>> profile = {
...     "name": "Sen",
...     "age": 18,
...     "sex": "male"
... }
>>> profile.keys()
dict_keys(['name', 'age', 'sex'])
>>> profile.values()
dict_values(['Sen', 18, 'male'])
>>> profile.items()
dict_items([('name', 'Sen'), ('age', 18), ('sex', 'male')])
>>> profile.get('phone', "10086")
'10086'
>>> profile.get('phone')
>>> profile['phone']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'phone'
>>> profile.pop("age")
18
>>> profile
{'name': 'Sen', 'sex': 'male'}
>>> profile.update({"age": 18})
>>> profile
{'name': 'Sen', 'sex': 'male', 'age': 18}
```

### 集合

#### 集合类型的常用操作符

```python
>>> all = set([1, 2, 3, 4, 5, 6, 7, 8, 8, 9, 10])
>>> odd = set([1,3,5,7,9])
>>> even = set([2,4,6,8,10])
>>> odd in all
False
>>> all
{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
>>> odd
{1, 3, 5, 7, 9}
>>> 3 in all
True
>>> odd < all
True
>>> odd <= all
True
>>> odd != all
True
>>> odd & all
{1, 3, 5, 7, 9}
>>> odd & even
set()
>>> odd | even == all
True
>>> odd | even
{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
>>> all - odd == even
True
>>> odd ^ even
{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
>>> odd ^ odd
set()
```

#### 集合类型的常用方法

```python
>>> odd.update(even)
>>> odd == all
True
>>> odd = all - even
>>> odd
{1, 3, 5, 7, 9}
>>> odd.add(11)
>>> odd
{1, 3, 5, 7, 9, 11}
>>> odd.discard(12)
>>> odd
{1, 3, 5, 7, 9, 11}
>>> odd.pop()
1
>>> odd.clear()
>>> odd
set()
```