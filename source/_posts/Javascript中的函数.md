---
title: 'Javascript中的函数'
date: 2018-05-28 14:52:14
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

### 函数的定义

1.   var foo = fucntion(a, b) {}; //函数表达式
2.  function foo(a, b) {}; //函数声明
3.  var foo = new Function("a", "b", "return false"); //不推荐

对于函数声明，可以在声明之前调用该函数；而对于函数表达式，不可以在表达式之前调用该函数。

### 函数的属性

*   this: 引用的是函数执行的环境对象，全局作用域中的this引用的是window对象
*   arguments: 保存函数参数的数组
*   arguments.callee: 指向该函数的指针
*   caller: 函数的调用者
*   length: 函数希望接收到的命名参数的个数

### 函数的方法

*   call(obj, arg1, arg2...)  //设置函数的运行环境（改变this的值)
*   apply(obj, [arg1, arg2]) //设置函数的运行环境（改变this的值)
*   bind(obj) //绑定函数的运行环境