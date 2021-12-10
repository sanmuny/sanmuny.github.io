---
title: 'Javascript中的函数表达式'
date: 2018-06-22 16:33:06
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
---

*   函数声明 有提升 而函数表达式没有，因此不应该通过if...else来声明函数
    
     
    if (condition) {
        function sayHi() {}
    } else {
        function sayHi() {}
    }
        
    
*   在递归函数中使用arguments.callee可以避免因函数名改变产生的错误。
    

    function factorial(num) {
        if (num <= 1) {
            return 1;
        } else {
            return num * arguments.callee(num - 1);
        }
    }

    
*   闭包是指函数中定义的函数，闭包的作用域链中包含父函数的执行环境（变量对象），所以即使父函数退出，如果闭包仍然被引用，那么父函数的变量对象就一直存在于内存中。所以，过度的使用闭包会导致内存占用过多
*   闭包的另外一个副作用是闭包只能取得父函数中变量的最后一个值，例如：
    function createFunctions() {
        var result = new Array();
        for(var i = 0; i < 10; i++) {
            result\[i\] = function() {
                return i;
            }
        }
        return result;
    }
    调用result中的每个函数都会返回10
*   闭包如果是匿名函数，则该函数中的this指向全局对象
*   可以用定义一个立即执行函数来模仿块级作用域：
    (function(){
    //块级作用域                                   
    })();