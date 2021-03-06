---
title: 'ES6: 对象扩展'
date: 2018-09-18 16:53:19
tags: [es6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

*   初始化简写： `function createPerson(name, age) { return { name: name, age: age }; } ---> function createPerson(name, age) { return { name, age }; } `
*   方法简写： `var person = { name: "sen", sayName: function() { return this.name; } }---> var person = { name: "sen", sayName() { return this.name; } } `
*   需要计算的属性名用[]表示
*   Object.is() 与===表现相同，除了 Object.is(NaN, NaN) // true Object.is(+0, -0) // false
*   Object.assign(target, source) 将source中的属性和方法混入target对象
*   允许重发的属性定义，排在最后的为实际值
*   属性枚举的顺序：
    *   所有数字类型的键按升序排列
    *   所有字符串类型的键按添加进对象的顺序排列
    *   所有符号类型的键也按照添加进对象的顺序排列
*   可修改对象的原型： Object.setPrototypeOf(obj, new_prototype)
*   使用super作为指向原型对象的指针