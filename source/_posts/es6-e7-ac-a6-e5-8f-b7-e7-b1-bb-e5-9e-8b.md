---
title: 'ES6: 符号类型'
date: 2018-10-15 16:12:58
tags: [ES6,javascript]
published: true
hideInList: false
feature: 
isTop: false
---

引入原因：

*   实现私有属性
    
    let s = Symbol()
    let obj = {[s]: 'hello world'};
    console.log(obj.s); //undefined
    
*   符号值唯一
    
    let s = Symbol()
    let m = Symbol()
    console.log(s === m)
    

创建：

*   不能用字面量形式创建
*   使用Symbol(desc)函数创建，不需要添加new
*   Symbol.for(key)在全局符号注册表中查找key符号值，如果找到就返回，没找到就创建一个新的符号值
*   Symbol.keyFor(symbol): 返回符号值的key

枚举：

*   符号类型的key是不能被枚举的，Object.keys()以及Object.getOwnPropertyNames()都不能返回符号类型的key
*   Object.getOwnPropertySymbols()则会返回对象中所有符号类型的key

共享符号值：

*   Symbol.for(desc): 在全局符号注册表中查找描述为desc的符号，如果找到，返回这个符号值，如果没有，则创建一个新的符号值并返回
*   Symbol.keyFor(var): 返回全局符号注册表中符号值为var的desc.

转换：

*   不能强制转化为字符串或者数值类型, 所以 symbol + "hello" 或者 symbol/1 会报错
*   可以调用String()来转换

知名符号：

*   Symbol.hasInstance(obj): 判断obj是不是当前函数的实例，如Array[Symbol.hasInstance](obj); 可以通过以下代码改变instanceof的默认行为：
    
    Object.defineProperty(MyObject, Symbol.hasInstance, {
        value: function(v) {
            return false;
        }
    });
    
*   Symbol.isConcatSpreadable
    
    let collection = {
        0: "Hello",
        1: "world",
        length: 2,
        [Symbol.isConcatSpreadable]: true
    };
    let messages = [ "Hi" ].concat(collection);
    console.log(messages.length); // 3
    console.log(messages); // ["hi","Hello","world"]
    
*   Symbol.match 、 Symbol.replace 、 Symbol.search 、Symbol.split
*   Symbol.toPrimitive
    
    function Temperature(degrees) {
        this.degrees = degrees;
    }
    Temperature.prototype[Symbol.toPrimitive] = function(hint) {
        switch (hint) {
        case "string":
            return this.degrees + "\\u00b0"; // 温度符号
        case "number":
            return this.degrees;
        case "default":
            return this.degrees + " degrees";
        }
    };
    let freezing = new Temperature(32);
    console.log(freezing + "!"); // "32 degrees!"
    console.log(freezing / 2); // 16
    console.log(String(freezing)); // "32°"
    
*   Symbol.toStringTag
    
    > age\[Symbol.toStringTag\] = 'Node';
    'Node'
    > age
    { age: 32,
    [Symbol(Symbol.toPrimitive)]: [Function],
    [Symbol(Symbol.toStringTag)]: 'Node' }
    > age.toString();
    '[object Node]'