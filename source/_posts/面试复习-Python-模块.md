---
title: 面试复习-Python-模块
tags:
  - python
categories: 计算机基础
published: true
hideInList: false
isTop: false
cover: /images/python.png
date: 2024-08-31 16:23:43
feature:
---

### 模块

模块是一个包含 Python 定义和语句的文件，其作用包括：

* 代码组织与复用
* 每个模块都有自己的全局作用域，不同模块中的同名变量和函数不会相互干扰

要点：

* 模块的搜索路径存在于PYTHONPATH环境变量中，解释器中可以通过`sys.path`查看
* 命名空间：局部空间 > 全局空间 > 内建空间
* python的对象可以看做一个命名空间，可以通过'.'给其添加属性。


### 模块导入

- `import module_name`
- `from module_name import obj1, obj2...`
- `from module_name import obj1 as obj`
- `import module_name as obj`
- 被导入模块只在第一次导入的时候执行
- 被导入的模块或Python对象遵循全局或者局部作用域的原则

### 包

包是一种组织模块的方式，它可以将多个相关的模块放在一个目录下，以便更好地管理和复用代码。一个包实际上是一个包含 __init__.py 文件的目录。这个文件可以是空的，也可以包含一些初始化代码，当包被导入时会自动执行。一个拥有子包的目录结构如下:
```python
my_package/
    __init__.py
    module1.py
    module2.py
    sub_package/
        __init__.py
        sub_module1.py
        sub_module2.py
```

要点：
- 默认绝对导入，即从包的根目录开始导入。相对导入指使用被导入模块的相对路径导入。相对导入只支持`from...import`的语法
``` python
# 从sub_module2中导入其他模块

from my_package.sub_package import sub_module1 # 绝对导入
from .sub_module1 import obj1 # 相对导入
from ..module1 import obj2 # 相对导入
```
- 模块中的对象命名如果以`_`开头在不会被`from module import *`导入

