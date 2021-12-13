---
title: 'Javascript中的数组'
date: 2017-11-16 00:03:55
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

*   数组的定义：
    *   var colors = new Array(20);
    *   var colors = new Array('red');  // \['red'\]
    *   var colors = \['red', 'green'\];
*   判断变量是不是数组：
    *   colors instanceof Array;  //true
    *   Array.isArray(colors); //true
*   将数组转化为字符串：
    *   colors.toString(); // 'red,green'
    *   colors.join(' '); //'red green'
    *   colors = \[ "red", undefined, "green", null, "yellow" \];
    colors.toString(); //"red,,green,,yellow"
    colors.join(' '); //"red  green  yellow"
    colors.join(';'); //"red;;green;;yellow"
            
*   栈方法
    *   colors.push('yellow'); //\['red', 'green', 'yellow'\]
    *   colors.pop(); //\['red', 'green'\]
*   队列方法
    *   colors.push('yellow'); //\['red', 'green', 'yellow'\]
    *   colors.shift(); //\['green', 'yellow'\]
    *   colors.unshift('black'); //\['black', 'red', 'green'\]
*   重新排序
    *   colors.reverse(); //\['green', 'red'\]
    *   colors.sort();
        *   按字符串升序排列
            *   var nums = \[1, 5, 10\]; //nums.sort();//\[1, 10, 5\]
        *   colors.sort(function(a,b){return a - b;});升序排列
        *   colors.sort(function(a,b){return b - a;});降序排列
*   操作
    *   var ret = colors.concat('black'); // ret = \['red', 'green', 'black'\]
    *   var ret = colors.slice(0, 1); //\['red'\] 切片
    *   colors.splice(pos, length, item, item...); 在pos处删除length项，然后插入item...
*   位置
    *   indexOf('red'); //0
    *   lastIndexOf('red'); //0
*   迭代
    *   colors.every(function(item, index, array){}); 所有元素返回true则返回true
    *   colors.filter(function(item, index, array){}); 返回为true的元素组成的数组
    *   colors.forEach(function(item, index, array){}); 无返回值
    *   colors.map(function(item, index, array){}); 返回函数调用结果组成的数组
    *   colors.some(function(item, index, array){}); 如果有一项返回true则返回true
*   归并方法
    *   var sum = values.reduce(function(prev, cur, index, array){return prev + cur}, 0);
    *   reduceRight(); 从后往前循环