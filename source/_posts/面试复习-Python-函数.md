---
title: 面试复习-Python-函数
tags:
  - python
categories: 计算机基础
published: true
hideInList: false
isTop: false
cover: /images/python.png
date: 2024-08-30 19:26:43
feature:
---

### 函数的参数组

``` python
>>> def myfunc(*args, **kwargs):
...     print(args)
...     print(kwargs)
...
>>> myfunc(1, 2, name="sen")
(1, 2)
{'name': 'sen'}
>>> myfunc(1, 2, name="sen", 3)
  File "<stdin>", line 1
    myfunc(1, 2, name="sen", 3)
                              ^
SyntaxError: positional argument follows keyword argument
>>> myfunc(name="sen")
()
{'name': 'sen'}
```

### 函数的属性
``` python
>>> myfunc.__doc__
>>> myfunc.version
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'function' object has no attribute 'version'
>>> myfunc.__
myfunc.__annotations__    myfunc.__hash__()
myfunc.__builtins__       myfunc.__init__(
myfunc.__call__(          myfunc.__init_subclass__(
myfunc.__class__(         myfunc.__kwdefaults__
myfunc.__closure__        myfunc.__le__(
myfunc.__code__           myfunc.__lt__(
myfunc.__defaults__       myfunc.__module__
myfunc.__delattr__(       myfunc.__name__
myfunc.__dict__           myfunc.__ne__(
myfunc.__dir__()          myfunc.__new__(
myfunc.__doc__            myfunc.__qualname__
myfunc.__eq__(            myfunc.__reduce__()
myfunc.__format__(        myfunc.__reduce_ex__(
myfunc.__ge__(            myfunc.__repr__()
myfunc.__get__(           myfunc.__setattr__(
myfunc.__getattribute__(  myfunc.__sizeof__()
myfunc.__getstate__()     myfunc.__str__()
myfunc.__globals__        myfunc.__subclasshook__(
myfunc.__gt__(            myfunc.__type_params__
>>> myfunc.__str__()
'<function myfunc at 0x104ce05e0>'
```

### 装饰器
``` python
def repeat(n):
  def dec_func(func):
    def wrapper(*args, **kwargs):
      for _ in range(n):
        result = func(*args, **kwargs)
      return result
    return wrapper
  return dec_func

@repeat(3)
def say_hello():
  print('hello')

say_hello()
```

### 可变长参数

``` python
>>> def my_function(*args, **kwargs):
...     for arg in args:
...         print(f"Position argument: {arg}")
...     for key, value in kwargs.items():
...         print(f"Keyword argument {key}: {value}")
...
>>> my_function(*(1,2,3,4,5), **{'name':'sen'})
Position argument: 1
Position argument: 2
Position argument: 3
Position argument: 4
Position argument: 5
Keyword argument name: sen
```

### 函数式编程用到的工具函数

``` python
>>> l = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> filter(lambda x:True if x > 5 else False, l)
<filter object at 0x102c47370>
>>> list(filter(lambda x:True if x > 5 else False, l))
[6, 7, 8, 9, 10]
>>> list(map(lambda x:x**2, l))
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
>>> from functools import reduce
>>> reduce(lambda x,y:x+y, l)
55
```

``` python
# 偏函数 与 柯里化
>>> from operator import add, mul
>>> from functools import partial
>>> add1 = partial(add, 1)
>>> add1(3)
4
>>> mul4 = partial(mul, 4)
>>> mul4(5)
20
>>> base2 = partial(int, base=2)
>>> base2('10010111')
151
```

### 变量作用域

局部变量通常会覆盖全局变量，可以在函数中通过`global`关键字引用全局变量

``` python
>>> def say_hello():
...   global foo
...   foo = 200
...   print(foo)
...
>>> foo = 100
>>> say_hello()
200
>>> foo
200
```

### 闭包

在 Python 中，闭包是一种函数，它可以访问其外部函数作用域中的变量，即使外部函数已经返回。

闭包由以下几个部分组成：

1. 外部函数：包含一个内部函数，并可能有一些局部变量。
2. 内部函数：定义在外部函数内部，引用外部函数的局部变量。
3. 外部函数的返回值：返回内部函数，使得内部函数可以在外部被调用。

闭包的作用：

- 数据隐藏和封装：

    闭包可以将一些数据隐藏在内部函数中，外部无法直接访问这些数据，只能通过闭包提供的接口来操作。
    例如，可以使用闭包来创建一个计数器，外部只能通过特定的函数来增加或读取计数器的值，而不能直接修改计数器的内部状态。

- 记忆功能：

    闭包可以记住外部函数的局部变量的值，即使外部函数已经返回。
    这在需要保存状态或实现记忆化等场景非常有用。例如，在计算斐波那契数列时，可以使用闭包来实现记忆化，避免重复计算。

- 实现面向对象编程的某些特性：

    闭包可以模拟类的属性和方法，通过闭包可以创建具有私有变量和方法的对象。
    例如，可以使用闭包来创建一个具有私有变量的对象，外部只能通过特定的方法来访问和修改这个私有变量。

``` python
>>> def fibonacci():
...     a, b = 0, 1
...     def inner():
...         nonlocal a, b
...         result = a
...         a, b = b, a + b
...         return result
...     return inner
...
>>> fib = fibonacci()
>>> for _ in range(10):
...     print(fib())
...
0
1
1
2
3
5
8
13
21
34
>>> fib()
55
```

### 生成器

生成器函数是一种包含yield关键字的函数。当调用生成器函数时，它不会立即执行函数体，而是返回一个生成器对象。每次调用生成器对象的__next__()方法或在循环中使用生成器时，函数会执行到下一个yield语句，暂停并返回一个值。然后，下次调用__next__()方法时，函数会从上次暂停的地方继续执行。

``` python
def my_generator():
    for i in range(5):
        yield i

gen = my_generator()
for num in gen:
    print(num)
```

### 协程

在 Python 中，协程是一种轻量级的并发编程方式，它允许在一个线程内实现多个任务的切换和协作，而不需要使用多线程或多进程。协程可以在等待某个事件发生时暂停执行，然后在事件发生后恢复执行，从而实现高效的并发处理.

优点：
* 高效的并发处理：协程可以在一个线程内实现多个任务的切换和协作，避免了传统线程之间的上下文切换开销，从而提高了并发处理的效率。
* 轻量级：协程是一种轻量级的并发编程方式，它不需要操作系统的线程调度和上下文切换，因此占用的资源较少。
* 易于编程：协程的编程模型相对简单，程序员可以自己控制执行流程，避免了传统线程之间的竞争和同步问题，从而降低了编程的难度。

``` python
>>> def print_received():
...   print('started')
...   x = yield
...   print('received: {}'.format(x))
...
>>> p = print_received()
>>> p.send(None)
started
>>> p.send(10)
received: 10
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```
