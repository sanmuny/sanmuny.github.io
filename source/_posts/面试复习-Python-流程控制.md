---
title: 面试复习-Python-流程控制
tags:
  - python
categories: 计算机基础
published: true
hideInList: false
isTop: false
cover: /images/python.png
date: 2024-08-28 19:26:42
feature:
---

### 分支语句

* `if-elif-else`

``` python
>>> string = 'hello world'
>>> string_len = len(string)
>>> if string_len > 10:
...     print("too long")
... elif 2 < string_len < 5:
...     print("okay")
... else:
...     print("too short")
...
too long
```

* 三目运算 `too_long = True if len(string) > 10 else False`

### 循环语句

* `for-in`
* `while`

* `break`
* `continue`

* `else`: 循环语句中的else在循环顺利完成后执行，即没有被`break`

### 迭代器

在 Python 中，迭代器是一种实现了迭代器协议的对象。迭代器协议包括两个方法：`__iter__()` 和 `__next__()`。

* 工作原理

可迭代对象（如列表、元组、字符串等）通过 `__iter__()` 方法返回一个迭代器对象。迭代器对象的 `__next__()` 方法用于逐个返回可迭代对象中的元素，当没有更多元素可返回时，会抛出 `StopIteration` 异常。

* 优点

节省内存：迭代器可以在需要的时候逐个生成元素，而不是一次性将所有元素加载到内存中。例如，对于一个非常大的文件或数据集合，使用迭代器可以避免内存溢出。实现懒加载：可以在实际需要的时候才计算和返回结果，提高程序的性能。

* 顺序类型可以通过`iter()`返回一个迭代器对象

* 字典可以按照其key来迭代：`for eachKey in dict`

* 文件可以按行来迭代：`for eachLine in file`

* 需要避免在迭代可变类型时修改该类型

* 示例

以下是一个简单的迭代器示例：

```python
class MyIterator:
    def __init__(self, data):
        self.data = data
        self.index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.index < len(self.data):
            result = self.data[self.index]
            self.index += 1
            return result
        else:
            raise StopIteration

my_list = [1, 2, 3, 4, 5]
my_iterator = MyIterator(my_list)

for item in my_iterator:
    print(item)

```

### 列表解析

* `[expr for iter_var in iterable]`
* `[expr for iter_var in iterable if condition]`
* `[(x+1, y+1) for x in range(3) for y in range(5)]`
* `[word for line in file for word in line.split()]`

### 生成器表达式

列表解析会立即生成一个完整的列表，并将其存储在内存中。如果处理的数据量很大，可能会占用大量内存。例如，`[x**2 for x in range(1000000)]`会创建一个包含一百万个元素的列表，占用较多内存。

生成器表达式返回一个生成器对象，它是一个可迭代对象，但不会立即生成所有的值，而是在需要的时候逐个生成值。这样可以节省内存，特别是处理大量数据时非常有用。例如，`(x**2 for x in range(1000000))`返回的生成器对象在迭代时才会逐个计算平方值，不会一次性占用大量内存。

* `(expr for iter_var in iterable)`