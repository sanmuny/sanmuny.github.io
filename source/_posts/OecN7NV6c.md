---
title: 'Javascript中的异步编程'
date: 2020-01-15 16:15:37
tags: [javascript,promise]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---
Javascript最开始是用于浏览器中的前端编程语言。Javascript是单线程的，为了能及时响应用户操作，javascript对耗时操作（如Ajax请求、本地文件读取等)的处理是异步进行的，也即是所谓的异步编程。除了快速响应用户操作之外，另外一个让javascript采用异步方式的原因是，程序无法预知用户会进行哪些操作。比如说程序无法提前知道用户是点“取消”按钮还是“确定”按钮。所以，Javascript采用了事件注册的方式来处理这个问题。在程序编写时，可以给用户点击“取消”按钮和“确认”按钮注册不同的回调函数，这样当用户点击不同的按钮时，不同的回调函数会被执行。本文从回调函数开始，介绍了Promise、async/await几种Javascript主要的异步编程方式。
<!-- more -->
# 异步编程和回调函数
无论是Ajax请求，还是事件处理，Javascript都是通过回调函数来完成的。谈及异步编程和回调函数，可以回想一下操作系统中的中断及中断处理程序。由于CPU的速度比外设快出许多，为了提高CPU的处理效率，计算机系统引入了中断的概念，外设在读写数据的时候，CPU可以忙别的事情，等到外设读写完数据后，会给CPU发一个中断信号，CPU就可以来执行已经注册好的、相应的中断处理程序。Javascript中的回调函数和中断处理程序都是类似的原理。

先来看一个异步的例子：

```
console.log("Start...");
setTimeout(()=>{
  console.log("in progress");
}, 2000);
console.log("End...");
```
如果是同步的话，输出的顺序应该是：
```
Start...
in progress
End...
```
然而真实的输出结果却是这样的：
```
Start...
End...
in progress
```
原因在于setTimeout中的第一个参数，箭头函数(即上文所说的回调函数)是异步执行的。setTimeout相当于注册一个回调函数，该回调函数在2000毫秒(2秒)之后运行。由于是异步的，主程序并不会等到两秒之后才跑setTimeout后面的代码，而是立即执行，所以先输出了`End...`，2秒之后，注册的回调函数运行了，输出了`in progress`。

举一反三，Ajax请求、事件处理都是类似的。比如：
```
$.ajax({
  url: url,
  data: data,
  success: ()=>{},
  dataType: dataType
});

$('#mydiv').on('click', ()=>{})
```
其中的两个箭头函数就是回调函数。

当后面的异步操作依赖于前面异步操作的结果时，就需要在回调函数中嵌套回调函数，例如：
```
console.log("Start...");
setTimeout(()=>{
  console.log('A');
  setTimeout(()=>{
    console.log('AB');
  });
}, 2000);
console.log("End...");
```
嵌套回调可以保证 `AB`一定在`A`之后输出。
```
Start...
End...
A
AB
```

回调函数是Javascript异步编程最基本的编写方式，但是容易遇到回调地狱的问题。所谓回调地狱，其实就是回调嵌套的太多，导致了代码难以阅读和编写。这是http://callbackhell.com/ 给出的一个例子：
```
fs.readdir(source, function (err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function (width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(dest + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
})
```

# Promise
为了解决回调地狱的问题，Promise被囊括到ES6中。Promise解决回调地狱问题的核心思想是：

1. 将异步操作的定义和对结果的处理分开来写
2. 对结果的处理可以串联

有点抽象，我们来看一个具体的例子。
```
console.log("Start...");
let waitOneSecond = new Promise(function(resolve, reject) {
  setTimeout(() => {
    let data = 1;
    resolve(data);
  }, 1000);
});
let waitTenSeconds = new Promise(function(resolve, reject) {
  setTimeout(() => {
    let data = 10;
    resolve(data);
  }, 10000);
});
console.log("Async operation registered...");

waitOneSecond
  .then(data => {
    console.log(`first output: ${data}`);
    return waitTenSeconds;
  })
  .then(data => {
    console.log(`second output: ${data}`);
  });

console.log("End...");
```
输出如下：
```
Start...
Async operation registered...
End...
first output: 1
second output: 10
```
上面的代码首先定义了两个异步操作：waitOneSecond和waitTenSeconds。分别是等待1秒和10秒和把1和10传给处理函数去处理。直到`console.log("Async operation registered...");`语句，两个异步操作都还没有开始。当执行到waitOneSecond.then时，异步操作才开始进行，主程序继续执行，输出了`End...`，1秒之后第一个then中注册的处理函数开始执行，输出了数字1，然后第二个异步操作waitTenSenconds.then开始执行，10秒后处理函数输出了数字10.

由此可以看到，两个异步操作的处理同样是先后执行，类似于上文例子中先打印`A`，后打印`AB`，引入Promise后就避免了嵌套回调，两个then函数调用串联起来，从而也就解决了回调地狱的问题。需要注意的是，要想将两个Promise串联起来的前提是，第一个Promise的处理函数必须返回一个Promise，如例子中的`return waitTenSeconds;`

除了解决回调地狱的问题，将异步操作定义和结果处理分开之后，我们可以更加灵活地处理多个异步操作。比如说，
```
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, 'foo');
});

Promise.all([promise1, promise2, promise3]).then(function(values) {
  console.log(values);
});
// expected output: Array [3, 42, "foo"]
```
promise1, promise2, promise3将会一起执行，如果都成功，我们可以在then函数中对所有的结果一起进行处理。

再例如：
```
const promise1 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 500, 'one');
});

const promise2 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 100, 'two');
});

Promise.any([promise1, promise2]).then(function(value) {
  console.log(value);
  // Both resolve, but promise2 is faster
});
// expected output: "two"
```
如果promise1和promise2有一个已经完成(无论成功或者失败)，就只处理这个已经完成的异步操作。

# async/await
ES6引入了迭代器和生成器，yield可以让程序暂停，而迭代器中的next()又可以程序恢复运行，利用这一点，Javascript便可以让主程序等待异步操作的完成。这对于习惯其他不使用异步编程语言(例如C语言)的同学来说就非常亲切了。而async/await正是利用迭代器和生成器编写异步函数的语法糖。例如：
```
let waitTenSeconds = new Promise(function(resolve, reject) {
  setTimeout(() => {
    let data = 10;
    resolve(data);
  }, 10000);
});

async function asyncFunc() {
  console.log("Start...");
  await waitTenSeconds.then(data => {
    console.log(data);
  });
  console.log("End...");
}

asyncFunc();
```
如果asyncFunc不是async/await函数的话，输出结果应该是:
```
Start...
End...
10
```
因为asyncFunc是异步操作，主程序会先打印`End...`，10秒之后才会打印`10`。而把asyncFunc改造为异步函数(即加了async关键字)之后，await关键字会让主程序等待waitTenSeconds异步操作执行完成之后才继续运行，所以输出结果是：
```
Start...
10
End...
```
所以，async函数的写法其实更像是同步函数。值得注意的是，这样的写法虽然更加直观明了，但Javascript的性能主要是靠异步操作来提升的，如果没有必要，是不建议使用await来等待的。

async/await语法如下：

* 需要在要异步函数前加上关键字async
* await只能用于async函数中
* async函数总是返回一个Promise
  
# 小结
随着Javascript语言的发展，异步编程的写法越来越简单明了，越来越灵活多样，但无论怎么变化，回调函数是Javascript实现异步操作最基本的语法，类似于中断机制的异步原理始终未变。无论技术如何发展，如何变化，但万变不离其宗，基本原理始终未变。
