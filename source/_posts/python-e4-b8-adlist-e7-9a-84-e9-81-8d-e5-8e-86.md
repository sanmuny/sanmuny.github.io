---
title: 'Python中list的遍历'
date: 2017-02-09 23:07:13
tags: [Python,列表遍历]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

在python中，若要遍历一个list而且需要在遍历时修改list，则需要十分注意，因为这样可能会导致死循环，例如：

    In [10]: ls = ['hello', 'world', 'bugggggggg']
    
    In [11]: for item in ls:
       ....:     if len(item) > 5:
       ....:         ls.insert(0, item)
       ....:         print ls
       ....:         
    ['bugggggggg', 'hello', 'world', 'bugggggggg']
    ['bugggggggg', 'bugggggggg', 'hello', 'world', 'bugggggggg']
    ['bugggggggg', 'bugggggggg', 'bugggggggg', 'hello', 'world', 'bugggggggg']
    ...

所以，为了安全起见，在遇到需要修改列表的时候，都不对列表本身进行遍历，而是创建一个列表的备份，然后对这个备份进行遍历，从而避免了上述情形。例如：

    In [20]: In [10]: ls = ['hello', 'world', 'bugggggggg']
    
    In [21]: for item in ls[:]:
       ....:     if len(item) > 5:
       ....:         ls.insert(0, item)
       ....:         
    
    In [22]: print(ls)
    ['bugggggggg', 'hello', 'world', 'bugggggggg']