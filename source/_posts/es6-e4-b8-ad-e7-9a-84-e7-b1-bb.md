---
title: 'ES6中的类'
date: 2019-01-02 17:43:54
tags: [ES6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
---

ES6中添加的class关键字其实并非真正的类，而是ES5用函数来模拟类的语法糖。在ES6中可以用如下的语法创建一个类：

    class Students {
        constructor(name, age) {
            this.name = name;
            this.age = age;
        }
        sayName() {
            console.log(this.name);
        }
        sayAge() {
            console.log(this.age);
        }
    }

    let st1 = new Students('sen', 18);
    st1.sayName();
    st1.sayAge();

使用ES6的class语法糖和ES5自定义的类还是有些区别的：

*   类的声明不会被提升，类的实例化只能在类的声明之后
*   类声明中的代码只能运行在严格模式下
*   类中的方法是不可枚举的
*   实例化的时候必须加new关键字
*   在方法内部修改类名会抛出错误，但可以在外部修改类名

下面的例子展示了如何在外部修改类名：

    class Students {
        constructor(name, age) {
            this.name = name;
            this.age = age;
        }
        sayName() {
            console.log(this.name);
        }
        sayAge() {
            console.log(this.age);
        }
    }

    let st1 = new Students('sen', 18);
    st1.sayName();
    st1.sayAge();

    let Stu = Students;

    let st2 = new Stu('sen', 18);
    st2.sayName();
    st2.sayAge();

    console.log(st1 instanceof Students); //true
    console.log(st2 instanceof Students); //true
    console.log(st2 instanceof Stu); //true
    console.log(st1 instanceof Stu); //true

#### 类中的访问器属性

可以使用get和set关键字来定义访问器属性，例如：

    class Students {
        constructor(name) {
            this.name = name;
        }
        get n() {
            return this.name;
        }
        set n(value) {
            this.name = value;
        }
    }

    let st1 = new Students('sen', 18);
    console.log(st1.n);
    st1.n = 'wang';
    console.log(st1.n);

#### 需计算的属性名

和其他需计算的名字一样，都是加上方括号，例如：

let propName = "sayName";

    class Students {
        constructor(name) {
            this.name = name;
        }
        [propName]() {
            console.log(this.name);
        }
    }

    let st1 = new Students('sen');
    st1.sayName();

#### 给类添加默认的生成器

可以利用Symbol来给类添加默认的生成器，如：

    class Collection {
        constructor() {
            this.items = [];
        }
        *[Symbol.iterator]() {
            yield *this.items.values();
        }
    }

    let col = new Collection();
    col.items.push(1);
    col.items.push(2);
    col.items.push(3);
    for( let i of col) {
        console.log(i);
    }

#### 给类添加静态属性/方法

类的静态方法/属性只能通过类名来访问，而不能通过类的实例来访问，具体做法是在方法/属性定义前面加上static关键字，例如：

    class Students {
        constructor(name) {
            this.name = name;
        }
        static sayClassName() {
            console.log("Collection");
        }
    }

    Students.sayClassName();
    let stu = new Students('sen');
    stu.sayClassName(); // error

#### 类的继承

ES6引入了extends和super来实现类的继承，例如：

    class Rectangle {
        constructor(length, width) {
            this.length = length;
            this.width = width;
        }
        getArea() {
            return this.length * this.width;
        }
    }

    class Square extends Rectangle {
        constructor(length) {
            super(length, length);
        }
    }

    let sq = new Square(5);
    console.log(sq.getArea());

使用super需要注意：

*   super只能用在派生类中
*   在constructor里，super负责初始化this，所以必须在this使用之前调用