---
title: 'ES6: 字符串处理'
date: 2018-07-24 14:33:21
tags: [es6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

UTF-16编码：

*   str.codePointAt(index)
*   String.fromCodePoint(codepoint);
*   normalize()

正则表达式

*   新增u修饰符用来处理UTF-16编码的问题  /&.$/u
*   新增pattern.flags 属性 // 'ig'
*   新增pattern.sticky 属性及'y'修饰符 // 只从lastIndex处匹配，如果和'g'一起存在则'g'被忽略
*   复制正则表达式  // let pattern = new RegEX(old_pattern, 'gi')

字符串处理

*   str.includes(sub_string) // true or false
*   str.startsWith(sub_string)  // true or false
*   str.endsWith(sub_string) // true or false
*   str.repeat(times);  // str * times
*   模板字面量
    *   支持多行字符串
    *   支持变量  // \`hello ${name}\`