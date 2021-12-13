---
title: 'ES6：数组'
date: 2019-01-03 16:25:11
tags: [ES6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

#### 新增的静态方法

Array.of() ：可以将传入的参数逐个传入数组，即使只有一个数值类型的参数，也会成为新数组的成员，而不是代表数组的长度 Array.from()：可以将类数组结构转化为数组，例如: Array.from(arguments). 利用Array.from()也可以转换原来的数组，例如：

    let trans_args = function() {
        let args = Array.from(arguments, (value)=>value + 1);
        console.log(args);
    }

    trans_args(1, 2, 3);

    $ node test.js 
    [ 2, 3, 4 ]

#### 新增的普通方法

find() 与findIndex()：传入一个回调函数来表明查找的条件，例如：

    > let arr = [1, 2, 3, 4, 5];
    undefined
    > arr.find((n)=>n>3);
    4
    > arr.findIndex((n)=>n>3);
    3

fill(value, start, end)：填充value到数组的start到end位置，不包括end. 例如：

    let arr = [1, 2, 3, 4, 5];
    arr.fill(1, 1, 4);
    [ 1, 1, 1, 1, 5 ]

copyWithin(toIndex, fromIndex, stopIndex): 从fromIndex开始复制元素到toIndex，遇到stopIndex停止，例如：

    > let arr = [1, 2, 3, 4, 5]
    undefined
    > arr.copyWithin(2, 0, 1);
    [ 1, 2, 1, 4, 5 ]

#### 类型化数组

javascript中的数组缓冲区类似于c的malloc，例如，可以用如下方法分配一个10个字节大小的内存区域：

    > let bu = new ArrayBuffer(10);
    undefined
    > bu.byteLength
    10

可以使用slice(start, end)方法来对分配的内存进行切片。 要操作新分配的内存，需要使用DataView(buf, index, length)创建一个视图，例如：

    > let buf = new ArrayBuffer(50);
    > let view = new DataView(buf, 0, 50);

获取视图的信息：

    > view.buffer === buf;
    true
    > view.byteOffset
    0
    > view.byteLength
    50

读写数据：

    > view.setUint8(4, 233);
    undefined
    > view.getUint8(4)
    233