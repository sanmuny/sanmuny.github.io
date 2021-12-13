---
title: 'Javascript中的基本包装类型'
date: 2018-05-29 15:40:15
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

当读取一个基本类型(Boolean、String、Number)的值时，后台对创建一个对应的基本包装类型，从而让我们可以调用一些方法来操作这些数据。 基本包装类型与引用类型的区别：

*   生存期。自动创建的包装类型只存在于一行代码存在的瞬间。不能在运行时为基本类型添加属性和方法。

### Number类型常用的方法

*   toString()
*   toFixed(2) //小数点后两位
*   toExponential()
*   toPrecision(3)

### String类型常用的属性和方法

*   length: 返回字符串的长度
*   charAt(2) charCodeAt(2): 返回字符串中第三个字符/编码
*   concat("xxx") 或者 + "xxx": 字符串拼接
*   slice(start, end) subString(start, end) subStr(start, number): 字符串切片 当start为负值时，slice和subStr会把它与字符串长度相加得到新的start;而subString则会将其转化为0 当end/number为负值时，slice会把它与字符串长度相加得到新的end，subStr和subString会将其转化为0
*   indexOf('d', start) 和lastIndexOf('d', start): 从start位置开始向左(右)查找'd'在字符串中的位置
*   trim(): 返回删除字符串左右的空格的新字符串
*   trimLeft() trimRight(): 返回删除字符串左(右)的空格的新字符串
*   toLowerCase() toLocaleLowerCase() toUpperCase() toLocaleUpperCase()
*   match(pattern/RegExp): 返回的数组与RegExp.exec相同
*   search(pattern/RegExp): 返回匹配到的位置
*   replace(old, new): 如果old为字符串时，仅替换该字符串；如果Old是带'g'标志的pattern时，会替换所有匹配的字符串 new字符串中可以是下列特殊序列： $$: $ $&: 等价于RegExp.lastMatch $': RegExp.leftContext $`: RegExp.rightContext $n $nn: 第n个捕获组匹配到的字符串 new可以是一个函数：function(match, pos, orignialText){return string}
*   var array = "string".split('string/pattern', number)