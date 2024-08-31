---
title: 面试复习-Python-面向对象
tags:
  - python
categories: 计算机基础
published: true
hideInList: false
isTop: false
cover: /images/python.png
date: 2024-08-31 17:20:40
feature:
---

### 面向对象编程的特点


* 封装是将数据和操作数据的方法封装在一个类中，对外隐藏内部的实现细节，只提供一些公共的方法来访问和修改数据。这样可以提高代码的安全性和可维护性，避免外部直接访问和修改内部数据，导致程序出现错误。

* 继承允许一个类（子类）继承另一个类（父类）的属性和方法，从而实现代码的复用和扩展。子类可以继承父类的所有公共属性和方法，并可以根据需要添加自己的属性和方法，或者重写父类的方法

* 多态是指同一个方法可以根据调用对象的不同而表现出不同的行为。在 Python 中，多态是通过方法重写和方法重载来实现的。方法重写是指子类重写父类的方法，方法重载是指在同一个类中定义多个同名方法，但参数列表不同。

* 抽象类是一种不能被实例化的类，它只能作为其他类的父类，用于定义一些抽象方法，这些方法没有具体的实现，需要在子类中实现
``` python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

    @abstractmethod
    def perimeter(self):
        pass
```

### Python类的基础知识

#### 类的定义

``` python
>>> class Student(object):
...   'student class'
...   def __init__(self, name, age):
...     self.name = name
...     self.age = age
...   def get_name(self):
...     return self.name
...   def get_age(self):
...     return self.age
...
>>> sen = Student('sen', 18)
>>> sen.get_name()
'sen'
>>> sen.get_age()
```

#### 子类的定义：
``` python
>>> class BoyStudent(Student):
...   def __init__(self, name, age):
...     super().__init__(name, age)
...     self.sex = 'male'
...   def get_sex(self):
...     return self.sex
...
>>> sen = BoyStudent('sen', 18)
>>> sen.get_sex()
'male'
```

#### 类的特殊属性

``` python
>>> BoyStudent.__doc__
>>> BoyStudent.__name__
'BoyStudent'
>>> BoyStudent.__bases__
(<class '__main__.Student'>,)
>>> BoyStudent.__dict__
mappingproxy({'__module__': '__main__', '__init__': <function BoyStudent.__init__ at 0x104681800>, 'get_sex': <function BoyStudent.get_sex at 0x104681620>, '__doc__': None})
>>> BoyStudent.__module__
'__main__'
>>> BoyStudent.__class__
<class 'type'>
>>> sen.__class__
<class '__main__.BoyStudent'>
```

#### 静态方法

在 Python 中，静态方法是一种不依赖于类实例和类本身的方法。它可以通过类名直接调用，而不需要创建类的实例。使用 @staticmethod 装饰器来定义静态方法。例如：
``` python
class MyClass:
    version = '1.0' # 静态属性
    @staticmethod
    def static_method():
        print("这是一个静态方法。")
MyClass.static_method() #调用静态方法
```

#### 类方法
使用 @classmethod 装饰器定义，并且第一个参数通常命名为 cls，代表类本身。

类方法：

可以访问类变量，通过 cls 参数可以访问和修改类的属性。不能直接访问实例变量，因为没有实例对象的引用。

静态方法：

不能访问类变量和实例变量，它完全独立于类和实例的状态。通常用于与类或实例没有直接关系的通用功能。

``` python
class MyClass:
    class_variable = 0

    def __init__(self, instance_variable):
        self.instance_variable = instance_variable

    @classmethod
    def class_method(cls):
        print(f"Class variable: {cls.class_variable}")

    @staticmethod
    def static_method():
        print("This is a static method.")
MyClass.class_method()
MyClass.static_method()

obj = MyClass(10)
obj.class_method()
obj.static_method()
```

#### 其他要点

* 组合类似于js中类的聚合，新类直接将旧类的实例包含在自己的属性中，从而达到访问旧类中属性和方法的目的
* Python的多重继承在查找属性和方法是广度优先搜索的

### 类相关的内建函数

``` python
>>> issubclass(BoyStudent, Student)
True
>>> isinstance(sen, BoyStudent)
True
>>> hasattr(BoyStudent, 'name')
False
>>> hasattr(sen, 'name')
True
>>> dir(Student)
['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getstate__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'get_age', 'get_name']
>>> hasattr(Student, 'get_name')
True
>>> getattr(sen, 'name')
'sen'
>>> setattr(sen, 'name', 'sen001')
>>> getattr(sen, 'name')
'sen001'
>>> delattr(sen, 'name')
>>> getattr(sen, 'name')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'BoyStudent' object has no attribute 'name'
>>> vars(sen)
{'age': 18, 'sex': 'male'}
```

### 类的特殊属性和方法

#### __slots__

* 当定义了 __slots__ 时，Python 不会为每个实例创建一个 __dict__ 属性字典来存储实例的属性。这可以显著减少内存使用，特别是在创建大量实例的情况下。
* __slots__ 可以明确地指定一个实例可以拥有的属性列表

``` python
>>> class BoyStudent(Student):
...   'boy student'
...   __slots__ = ['name', 'age', 'sex']
...   def __init__(self, name, age):
...     super().__init__(name, age)
...     self.sex = 'male'
...   def get_sex(self):
...     return self.sex
...
>>> sen = BoyStudent('sen', 18)
>>> sen.phone = 123 # 没有限制住，因为父类中没有__slot__，所以父类中存在__dict__
>>> vars(sen) # 新的属性存进了父类的__dict__
{'phone': 123}

>>> class Student(object):
...   def __init__(self, name, age):
...     self.name = name
...     self.age = age
...   __slots__ = ['name', 'age']
...
>>> sen = Student('sen', 18)
>>> sen.phone = 123
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'phone'
AttributeError: 'Student' object has no attribute 'phone'
>>> vars(sen)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: vars() argument must have __dict__ attribute
```

#### 描述符方法 __get__ __set__ __delete__

在 Python 中，描述符是一种实现了特定协议（`__get__`、`__set__`和`__delete__`方法）的对象，用于控制对另一个对象属性的访问。主要用于自定义
实例属性访问时的一些操作。

``` python
class IntegerValidator:
  def __set_name__(self, owner, name):
    self.name = name
  def __set__(self, instance, value):
    if not isinstance(value, int):
      raise ValueError(f'{self.name} must be an integer.')
    instance.__dict__[self.name] = value
  def __get__(self, instance, owner):
    if instance is None:
      return self
    return instance.__dict__.get(self.name)
  def __delete__(self, instance):
    print(f"Deleting {self.name} from {instance}")
    del instance.__dict__[self.name]

class Student:
  age = IntegerValidator()

>>> s = Student()
>>> s.age = '28'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 6, in __set__
ValueError: age must be an integer.
>>> s.age = 28
>>> s.age
28
>>> del s.age
Deleting age from <__main__.Student object at 0x1007714f0>
```
