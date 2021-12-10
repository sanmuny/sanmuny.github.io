---
title: 'Javascript中的单体内置对象'
date: 2018-05-29 16:00:36
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
---

### Global

1.  encodeURI() encodeURIComponent(): 用utf-8编码替换URI中的无效字符，区别在于encodeURI并不对属于URI的特殊字符进行编码，如:// #?等
2.  eval("var msg='hello world'"); alert(msg);
3.  Global对象的属性：
    *   undefined
    *   NaN
    *   Infinity
    *   Object
    *   Array
    *   Function
    *   Boolean
    *   String
    *   Number
    *   Date
    *   RegExp
    *   Error
    *   EvalError
    *   RangeError
    *   ReferenceError
    *   SyntaxError
    *   TypeError
    *   URIError
4.  可通过window对象来访问global对象的变量和函数
5.  var global = function(){return this;}();

### Math

*   Math对象的属性：
    *   Math.E
    *   Math.LN10
    *   Math.LN2
    *   Math.LOG2E
    *   Math.LOG10E
    *   Math.PI
    *   Math.SQRT1_2
    *   Math.SQRT2
*   Math.min(\[1, 2, 3\]) Math.max(\[1, 2, 3\])
*   Math.ceil()
*   Math.floor()
*   Math.round()
*   Math.random():  \[0-1)
*   Math.abs()
*   Math.exp()
*   Math.log()
*   Math.pow(num, power)
*   Math.sqrt()
*   Math.acos(x)
*   Math.asin(x)
*   Math.atan(x)
*   Math.atan2(y, x)
*   Math.cos(x)
*   Math.sin(x)
*   Math.tan(x)