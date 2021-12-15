---
title: 'Javascript中的事件'
date: 2018-07-18 11:15:24
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

事件捕获(capturing)和事件冒泡(bubbling) 

添加事件处理程序的两种方法

*   <div onclick="alert('ddd')"></div>  // onclick=null
*   elem.addEventListener("click", e\_func, false); //elem.removeEventListener('click', e\_func, false); //false 表明在冒泡阶段被触发

事件对象

*   e.bubbles // true or false 是否冒泡
*   e.cancelable // true of false 是否可取消默认行为
*   e.currentTarget // 正在处理事件的元素，事件处理程序中的this指向该元素
*   e.target // 事件发生的目标元素
*   e.defaultPrevented // true or false 默认行为是否被取消
*   e.detail
*   e.eventPhase // 1 捕获阶段 2 处于目标 3冒泡阶段
*   e.preventDefault()
*   e.stopImmediatePropagation() //阻止事件冒泡和所有事件处理程序
*   e.stopPropagation() //阻止事件冒泡
*   e.trusted // true 表示事件由浏览器生成，false表示有javascript触发
*   e.type
*   e.view
*   e.clientX e.clientY 事件发生时鼠标的视图位置
*   e.pageX e.pageY 事件发生时鼠标的页面位置
*   e.screenX e.screenY 事件发生时鼠标的屏幕位置
*   e.ctrlKey e.shiftKey e.altKey e.metaKey 事件发生时修饰键是否被按下
*   e.button // 0 主键 1 滚轮键 2右键 被按下
*   e.wheelDelta
*   e.charCode
*   e.keyCode
*   e.data //textinput 事件时输入的字符
*   e.iputMethod //0-9 输入来源，如键盘、粘贴、拖放等

事件类型

*   UI事件
    *   load  (window, img)
    *   unload
    *   resize (window)
    *   scroll (window)
*   焦点事件
    *   blur 失去焦点事触发，不会冒泡
    *   focusin
    *   focusout
    *   focus 不会冒泡
*   鼠标与滚轮事件
    *   click
    *   dbclick
    *   mousedown 任何鼠标按键被按下时触发
    *   mouseup
    *   mouseenter 不冒泡
    *   mouseover 冒泡
    *   mousemove
    *   mouseleave 不冒泡
    *   mouseout 冒泡
    *   mousewheel
    *   contextmenu 鼠标右键弹出菜单
*   键盘事件
    *   keydown
    *   keypress
    *   keyup
    *   textinput
*   DOM变动事件
    *   DOMSubtreeModified
    *   DOMNodeInserted
    *   DOMNodeRemoved
    *   DOMNodeInsertedIntoDocument
    *   DOMNodeRemovedFromDocument
    *   DOMAttrModified
    *   DOMCharacterDataModified

事件委托和移除 为提升性能，利用事件冒泡来减少事件处理程序；在DOM元素被innerHTML移除后，需要手动的移除事件处理程序 事件模拟

*   var e = document.createEvent('MouseEvents');
*   e.initMouseEvent(....);
*   elem.dispatchEvent(e);