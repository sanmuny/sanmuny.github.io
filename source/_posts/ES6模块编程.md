---
title: 'ES6: 模块编程'
date: 2019-01-08 21:34:59
tags: [es6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

#### Javascript模块的限制

*   只能运行于严格模式
*   模块中的顶级作用域中的变量，不会被自动添加到全局作用域
*   顶级作用域的this为undefined

#### 导出

如果想让模块中的变量、函数、类被其他模块使用，需要将其导出，导出的方法如下：

*   export var color = "red";
*   export function print_hello(){};
*   export { print_hello }
*   export { print_hello as printh };
*   export default function print_hello(){};
*   export default print_hello
*   export { print_hello as default}

#### 导入

如果想使用其他模块中的变量、函数、类，需要将其导入。导入后的变量、类、函数为只读。导入的方法如下：

*   import { color, print_hello } from "./example.js";
*   import * as example from "./example.js";  //example.color, example.print_hello
*   import { print_hello as printh } from "./example.js";
*   import print\_hello, { color } from "./example.js"; // print\_hello 为默认导出的函数
*   import { default as printh } from "./example.js";