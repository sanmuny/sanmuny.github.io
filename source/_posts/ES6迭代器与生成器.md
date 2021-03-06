---
title: 'ES6: 迭代器与生成器'
date: 2018-12-07 16:23:24
tags: [es6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

#### 什么是迭代器？

迭代器是一个对象，它拥有一个next方法，调用next方法会返回一个对象，该对象有两个属性值，value和done。每次调用next方法，返回的value表示可迭代对象中的下一个值，done表示迭代是否完成。根据此定义，可用下面的代码实现一个迭代器:

    let create_iter = function(items) {
        let i = 0;
        return {
            next: function() {
                let value, done;
                if (i >= items.length) {
                    value = null;
                    done = true;
                } else {
                    value = items\[i++\];
                    done = false;
                }
                return {
                    value: value,
                    done: done
                }
            }
        }
    }

#### 什么是生成器？

用来创建迭代器的函数称之为生成器，ES6为了简化生成器，引入了新的语法：

*   在生成器函数前加*
*   使用yield关键字抛出下一个value

引入新的语法后，生成器的代码可以简化为：

    let gen = function*(items) {
        for (let i = 0; i < items.length; i++) {
            yield items\[i\];
        }
    }

#### 举个栗子

下面的代码比较了两种创建迭代器的方法：

    let create_iter = function(items) {
        let i = 0;
        return {
            next: function() {
                let value, done;
                if (i >= items.length) {
                    value = null;
                    done = true;
                } else {
                    value = items[i++];
                    done = false;
                }
                return {
                    value: value,
                    done: done
                }
            }
        }
    }

    let gen = function*(items) {
        for (let i = 0; i < items.length; i++) {
            yield items[i];
        }
    }

    let items = [1, 2, 3, 4, 5];

    let it = gen(items);
    console.log(it.next());
    console.log(it.next());
    console.log(it.next());
    console.log(it.next());
    console.log(it.next());
    console.log(it.next());

输出结果：

    $ node test.js 
    { value: 1, done: false }
    { value: 2, done: false }
    { value: 3, done: false }
    { value: 4, done: false }
    { value: 5, done: false }
    { value: undefined, done: true }
    { value: undefined, done: true }

#### 生成器的表现形式

*   function *my_generator(items) {}
*   let my_generator = function *(items) {}
*   let obj = {*my_generator(items){}};

#### 可迭代对象

可迭代对象指的是包含Symbol.iterator属性的对象，数组、Set、Map、字符串都是可迭代对象，都有默认的迭代器。 可迭代对象可以与for-of配合使用，例如：

    let numbers = [1, 2, 3, 4, 5];
    for (let num of numbers) {
        console.log(num);
    }

可以用一下方法自定义一个可迭代对象：

    let colors = {
        items: [1, 2, 3, 4, 5],
        *[Symbol.iterator]() {
            for (let item of this.items) {
                yield item;
            }
        }
    }

#### 内置迭代器

数组、Map和Set 都有一下三个内置的迭代器：

*   entries(): 返回一个包含键值对(数组)的迭代器
*   values(): 返回一个只包含值的迭代器
*   keys(): 返回一个只包含键的迭代器

对于数组来说，keys返回的是元素的下标，对于Set来说，返回的是元素的值，而对于Map来说，返回的是元素的key。

#### 给迭代器传入参数

当调用next方法时，若传入参数，则该参数会替代上一次yield表达式的值，例如：

    let gen = function *() {
        let a = yield 1;
        let b = yield a + 1;
        yield b + 1;
    }

    let iter = gen();

    console.log(iter.next());
    console.log(iter.next(100));
    console.log(iter.next(200));
    console.log(iter.next());

    执行结果：

    $ node test.js 
    { value: 1, done: false }
    { value: 101, done: false }
    { value: 201, done: false }
    { value: undefined, done: true }

    #### 生成器委托

    也就是生成器的嵌套，例如：

    let generator0 = function *() {
        yield 0;
        yield 1;
        yield 2;
    }

    let generator1 = function *() {
        yield 3;
        yield 4;
        yield 5;
    }

    let generator = function *() {
        yield *generator0();
        yield *generator1();
        yield 6;
    }

    let iter = generator();

    console.log(iter.next());
    console.log(iter.next());
    console.log(iter.next());
    console.log(iter.next());
    console.log(iter.next());
    console.log(iter.next());
    console.log(iter.next());
    console.log(iter.next());