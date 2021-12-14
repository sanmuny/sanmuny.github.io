---
title: 'ES6: 函数扩展'
date: 2018-08-20 16:39:49
tags: [es6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

*   带默认值参数的函数：var get_name = function(url, id=1, callback){};
    *   如果传入了第二个参数，将不会使用默认值
    *   如果给第二个参数赋值为undefined，会使用默认值
    *   带有默认值的参数后，arguments的内容将不会随着形参的改变而改变
    *   排在后面的参数可以将前面的参数作为默认值，而前面的参数不能引用后面的参数作为默认值
*   剩余参数：var get_name = function(url, ...keys)
    *   除了第一个参数url外，剩余所有参数都会被放到keys数组中
    *   函数只能有一个剩余参数，且必须放到最后
    *   剩余参数不能在对象字面量的setter属性中使用
*   延展运算符：`var args = ['url', 123, 'st']; get_names(...args);`
*   new.target: 使用new创建一个对象时，会被赋值为新对象的构造器
*   ES6允许在代码块中定义函数，在严格模式中，块级函数只能在块级作用域中使用，而非严格模式中，块级函数会被提升到全局
*   箭头函数
    *   没有this,super,arguments,new.target,这些值由所在的、最靠近的非箭头函数来决定
    *   不能使用new来调用
    *   没有原型
    *   不能更改this
    *   不允许有重复的具名参数
    *   语法
        *   `var reflect = value => value;`
        *   `var sum = (num1, num2) => num1 + num2;`
        *   `var getName = () => 'Nicholas';`
        *   `var sum = (num1, num2) => {return num1 + num2};`
        *   `var doSomething = () => {};`
        *   `var getItem = (id) => ({id: id, name: 'Nicholas'});`
        *   `var person = ((name) => {return {getName: function() {return name}};})`
*   尾调用优化：满足以下条件的尾调用将会被优化，在尾调用时不会创建新的栈帧，而是清除当前栈帧并再次利用它
    *   尾调用不能引用当前栈帧中的变量(意味着该函数不能是闭包)
    *   进行尾调用的函数在尾调用返回结果后不能做额外操作
    *   尾调用的结果作为当前函数的返回值