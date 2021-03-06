---
title: 'DOM中的选择符API及元素遍历'
date: 2018-07-11 14:15:22
tags: [dom,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
---

### 选择符API

*   document.querySelector(css\_selector) var checked\_radio = document.querySelector('.checked');
*   var node\_list = document.querySelector(css\_selector);
*   elem.matchesSelector(css_selector) // true or false

### HTML 5中的类名操作

classList是DOMTokenList的实例，因而具有以下方法：

*   elem.classList.add(value); 如果已经有了就不添加了
*   elem.classList.contain(value) //true or false
*   elem.classList.remove(value)
*   elem.classList.toggle(value); 如果有就删除，如果没有就添加

###  元素遍历

Element新增5个属性：

*   childElementCount: 子元素的个数（不包括Text和Comment类型）
*   firstElementChild
*   lastElementChild
*   previousElementSibling
*   nextElementSibling