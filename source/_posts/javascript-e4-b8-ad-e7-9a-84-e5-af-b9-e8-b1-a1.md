---
title: 'Javascript中的对象'
date: 2018-06-20 17:53:39
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
---

属性的类型
-----

### 1.1 数据类属性

数据类属性的属性——特性为：

*   [[Configurable]]: 默认为true。表示能否通过delete删除属性，能否修改属性的特性。
*   [[Enumerable]]: 默认为true。表示在用for-in迭代对象时，能否迭代出该属性。
*   [[Writable]]: 默认为true。表示能否修改属性的值。
*   [[Value]]: 默认为undefined。保存属性的值。

### 1.2 访问器类属性

访问器类属性的特性为：

*   [[Configurable]]: 默认为true。表示能否通过delete删除属性，能否修改属性的特性。
*   [[Enumerable]]: 默认为true。表示在用for-in迭代对象时，能否迭代出该属性。
*   [[Get]]: 默认为undefined。读取属性时调用的函数。
*   [[Set]]: 默认为undefined。写入属性时调用的函数。

使用访问器类型的属性的常用方法是写入该属性时往往会导致其他属性也跟着变化。  

### 1.3 对特性的操作方法：

Object.defineProperty(Object, propertyName, {attributes}): 设置属性的特性。 Object.defineProperties(Object, {propertyName: {attributes},}): 设置多个属性的特性。 Object.getOwnPropertyDescriptor(Object, propertyName);  

创建对象的方法
-------

### 2.1 工厂模式

 
    function createPerson(name, age, job) {
        var o = new Object();
        o.name = name;
        o.age = age;
        o.job = job;
        o.sayName = function() {
            alert(this.name);
        }
        return o;
    }

    var sen = createPerson('sen', 19, 'programer');

缺点：无法识别一个对象属于哪一个类

    alert(sen instanceof createPerson) // false

### 2.2 构造函数模式

    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = function() {
            alert(this.name);
        }
    }

    var sen = new Person("sen", 19, "programer");

    alert(sen instanceof Person) // true
    alert(sen.constructor === Person) // true

缺点：每个对象都会有独立的函数定义

    var sen1 = new Person("sen1", 19, "player");
    alert(sen1.sayName == sen.sayName) // false

### 2.3 原型模式

    function Person() {
    }

    Person.prototype.name = "sen";
    Person.prototype.age = "19";
    Person.prototype.job = "programer";
    Person.prototype.sayName = fucntion() {
        alert(this.name);
    }

    var sen = new Person();

    Person.prototype.constructor === Person // true
    Person.prototype.isPrototypeOf(sen) // true

    Object.getPrototypeOf(sen) === Person.prototype // true

    sen.hasOwnProperty("name") // false
    "name" in sen // true

判断属性是不是在原型对象中：

    !sen.hasOwnProperty("name") && ("name" in sen)

原型模式更简洁的写法：

    function Person(){};

    Person.prototype = {
        constructor = Person,
        name: "sen",
        age: 19,
        job: "programer",
        sayName: function() {
            alert(this.name);
        }
    }

    Object.defineProperty(Person.prototype, {
        enumerable: false,
        value: Person
    });

缺点： 1. 省略了传递初始化参数 2. 如果原型中的属性为引用类型，则所有对象会共用这个引用类型

### 2.4 组合使用构造模式和原型模式

    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
    }

    Person.prototype.sayName = function() {
        alert(this.name)
    }

缺点： 构造函数和原型是独立的

### 2.5 动态原型模式

    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;

        if (typeof this.sayName !== "function") {
            Person.prototype.sayName = function() {
                alert(this.name);
            };
        }
    }

### 2.6 寄生构造函数模式

    function Person(name, age, job) {
        var o = new Object();
        o.name = name;
        o.age = age;
        o.job = job;
        o.sayName = function() {
            alert(this.name);
        }
        return o;
    }

    var sen = new Person('sen', 19, 'programer');

寄生构造函数模式一般用于创建特殊的引用类型。例如，创建一个包含toPipedString方法的数组类型。

    function SpecialArray() {
        var arr = new Array();
        arr.push.apply(arr, arguments);
        arr.toPipedString = function() {
            return this.join(" | ");
        }
        return arr;
    }

    var colors = new SpecialArray("red", "black");
    alert(colors.toPipedString());

缺点和工厂模式一样，都无法被instanceof识别

### 2.7 稳妥构造函数模式

与寄生构造函数类似，区别在于：

*   不使用this给类添加公共属性
*   创建对象时不使用new关键字

    function Person(name, age, job) {
        var o = new Object();
        o.sayName = function() {
            alert(name);
        }
        return o;
    }

    var sen = Person('sen', 19, 'programer');

缺点和工厂模式一样，都无法被instanceof识别

继承
--

### 3.1 原型链

    function SuperType() {
        this.property = true;
    }

    SuperType.prototype.getSuperValue = function() {
        return this.property;
    }

    function SubType() {
        this.subProperty = false;
    }

    SubType.prototype = new SuperType();

    SubType.prototype.getSubValue = function() {
        return this.subProperty;
    }

    var obj = new SubType();

    alert(obj instanceof SubType) // true
    alert(obj instanceof SuperType) // true
    alert(obj instanceof Object) // true

缺点：

*   SuperType中的属性如果为引用类型，所以的子类都会共用该属性
*   创建子类时，如果给父类传递参数，会影响其他的子类

### 3.2 构造函数继承继承

    function SuperType() {
        this.colors = ['black', 'red'];
    }

    function SubType() {
        SuperType.call(this);
    }

缺点：函数无法复用

### 3.3 组合继承

    function SuperType(name) {
        this.name = name;
        this.colors = ['black', 'red'];
    }

    SuperType.prototype.sayName = function() {
        return this.name;
    }

    function SubType(name) {
        SuperType.call(this, name);
    }

    SubType.prototype = new SuperType();

### 3.4 原型式继承

    var person = {
        name: "sen"
    }

    var newPerson = Object.create(person, {name: {
        value: "yang"
    }});

缺点： 原型式继承仅仅是对父类做了一个浅拷贝，并加入了子类自己的属性。所以问题和原型链相同，父类中的引用类型属性会被所有子类共用。

### 3.5 寄生式继承

    function createAnother(obj) {
        var clone = Object.create(obj);
            clone.sayHi = function() {
            alert('hi');
        }
        return clone;
    }

缺点： 函数不能复用

### 3.6 寄生组合式继承 (常用)

    function inheritPrototype(subType, superType) {
        var prototype = Object.create(superType.prototype);
        prototype.constructor = subType;
        subType.prototype = prototype;
    }

    function SuperType(name) {
        this.name = name;
        this.colors = ['red', 'black'];
    }

    SuperType.prototype.sayName = function() {
        alert(this.name);
    }

    function SubType(name) {
        SuperType.call(this, name);
        this.age = age;
    }

    inheritPrototype(SubType, SuperType);