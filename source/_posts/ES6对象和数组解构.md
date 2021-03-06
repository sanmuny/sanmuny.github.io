---
title: 'ES6: 对象和数组解构'
date: 2018-09-18 17:35:13
tags: [es6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

所谓解构，指的是将数据结构分解为更小的部分，从而使数据提取变得容易。 对象解构：

*   使用解构时，必须提供初始化值 `let Person = { name: 'sen', age: 18 } let {name, age} = Person;`
*   解构表达式的值为=右侧的值
*   如果对象中没有同名属性时，解构表达式新赋值的变量的值为undefined
*   解构对象只是赋值时，需要加()
*   赋值给不同名的变量 `let Person = { name: 'sen', age: 18 } let {name: localName, age: localAge} = Person;`
*   设置默认值 `let Person = { name: 'sen', age: 18 } let {name, age: localAge = 18} = Person; `
*   嵌套解构 `let Person = { name: 'sen', age: 18, score: { maths: 100 } } let {name, score: {maths}} = Person; console.log(maths); `

数组解构

*   `let score = [99, 88, 77]; let [maths, english, chinese] = score; [,,chinese] = score;`
*   数组解构赋值不需要加()  `let a = 3; let b = 4; [a, b] = [b, a] `
*   嵌套的解构 `let score = [99, 88, [77]]; let [,,[chinese]] = score; `
*   剩余项 `let score = [99, 88, 77]; let [maths, ...restScore] = score; console.log(restScore) // [88, 77] `
*   数组和对象可以混合解构` let Person = { name: 'sen', age: [0, 18] } let {name: localName, age: [start]} = Person; console.log(start); `