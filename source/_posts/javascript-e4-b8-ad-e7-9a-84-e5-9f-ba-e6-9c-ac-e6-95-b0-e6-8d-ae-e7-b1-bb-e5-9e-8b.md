---
title: 'Javascript中的基本数据类型'
date: 2017-11-15 00:09:51
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
---

Undefined
=========

*   在var或者let中声明了变量但没有赋值时，这个变量的值就是undefined.
*   使用typeof关键字检测未声明变量的类型为undefined.

Null
====

*   null表示一个空对象指针，所以用typeof检测null时，会返回object
*   undefine派生自null, null == undefined 为true, null === undefined为false

Boolean
=======

*   true false 区分大小写
*   空字符串、0和NaN、null、undefined转换为boolean的值为false

Number
======

*   Number表示整数和浮点数
*   八进制数以0开头，十六进制数以0x开头
*   Number.MIN\_VALUE 表示Javascript支持的正的最小数值，Number.MAX\_VALUE表示Javascript支持的最大数值
*   超出最大数值就会被转化为Infinity，如果为负值则会被转化为-Infinity
*   isFinite()函数可以判断一个数值是否在支持的范围之内
*   NaN表示本来该返回数值的操作数未返回数值的情况，如除以0就会返回NaN
*   NaN的数值运算会返回NaN
*   NaN == NaN 为false
*   isNaN()函数可以判断一个数值是不是NaN
*   Number()函数可以将其他类型的值转换为Number类型：
    *   Number(true) = 1; Number(false) = 0;
    *   Number(null) = 0;
    *   Number("") = 0; Number("0x1a") = 26; Number('076') = 76;
    *   Number('100hello') = NaN;
    *   如果是对象，则会调用对象的valueOf()方法，如果返回的是NaN则会先调用toString方法转化为字符串，然后根据字符串的转换规则来转换
*   parseInt()函数：
    *   parseInt('100hello') = 100;
    *   parseInt('') = NaN;
    *   parseInt('0x1a') = 26;
    *   parseInt('076') = 76; parseInt('076', 8) = 62;
*   parseFloat()与parseInt()类似，但有如下区别：
    *   parseFloat不能传入第二个参数（进制），不能解析十六进制字符串

String
======

*   字符串一旦创建，其值不能改变，如：var lang = 'Java'; lang += 'Script'; 会重新创建一个字符串，填充上'JavaScript', 'Java'和'Script'都将被销毁
*   除了null和undefined之外，其他的几个数据类型都有toString()方法，可以将其转换为字符串
*   数值类型调用toString()方法可以传入进制作为参数，如：var a=20; a.toString(2);
*   String()方法可以将null转化为'null'，把undefined转化为'undefined'