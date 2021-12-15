---
title: 'Javascript中的Date'
date: 2018-05-17 14:57:02
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

1. Date类型的创建

*   var date = new Date() //获取当前时间
*   var date = new Date(ms) //根据Unix时间戳来创建Date对象
*   var date = new Date(Date.parse("string")) //string为日期时间组成的字符串，符合要求的字符串格式包括：
    *   “m/d/y”
    *   "January 12, 2004"
    *   "Tue May 25 2004 00:00:00 GMT-0700"
    *   YYYY-MM-DDTHH:mm:ss.sssZ"
*   var date = new Date(Date.UTC()) // Date.UTC的参数为年、月、日、小时、分钟、秒、毫秒

2. Date类型的操作 var date = new Date();

*   Date.now() 或者+new Date()获取当前的时间戳, "+"操作符可获取Date对象的时间戳
*   date.toDateString(); // "Thu May 17 2018"
*   date.toTimeString(); // "14:37:29 GMT+0800 (CST)"
*   date.toLocaleDateString(); // "2018/5/17"
*   date.toLocaleTimeString(); // "下午2:37:29"
*   date.toUTCString(); // "Thu, 17 May 2018 06:37:29 GMT"
*   date.toLocaleString(); // "2018/5/17 下午2:37:29"
*   date.getTime(); // 1526539049298
*   +date; // 1526539049298
*   date.setTime(1526539049300);
*   date.getFullYear(); // 2018
*   date.getUTCFullYear(); // 2018
*   date.getMonth(); // 0-11
*   date.getUTCMonth(); // 0-11
*   date.getDate(); //1-31
*   date.getUTCDate(); // 1-31
*   date.getDay(); // 0-6 0表示周日
*   date.getUTCDay(); // 0-6 0表示周日
*   date.getHours(); // 0-23
*   date.getUTCHours(); // 0-23
*   date.getMinutes(); // 0-59
*   date.getUTCMinutes(); // 0-59
*   date.getSeconds(); // 0-59
*   date.getUTCSeconds(); // 0-59
*   date.getMilliseconds(); // 0-999
*   date.getUTCMilliseconds(); // 0-999
*   date.getTimezoneOffset(); // -480