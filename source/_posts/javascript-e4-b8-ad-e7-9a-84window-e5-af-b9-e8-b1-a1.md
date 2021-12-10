---
title: 'Javascript中的window对象'
date: 2018-06-23 18:50:53
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
---

window对象的两个作用：

*   表示浏览器的一个实例
*   ECMAscript中的Global对象 直接定义Global变量与在window上定义Global变量的区别是：直接定义的Global变量的\[\[configurable\]\]特性为false，也就是说，它不能用delete删除

窗口关系及框架

*   如果html中包含框架(frame)，那么每个框架都有自己的window对象，它们从上到下，从左到右，依次存放于window.frames数组中。可以通过window.frames\[index\]或者window.frames\[frame_name\]来获取frame的window对象
*   window.name为frame的name
*   top对象表示最外层html的window对象
*   parent对象表示上层frame的window对象
*   self表示frame自身的window对象

窗口位置

*   window.screenLeft window.screenTop 或者firefox中的window.screenX window.screenY表示窗口左上角的位置
    
    var left = (typeof window.screenLeft == "number") ? window.screenLeft: window.screenX;
    var top = (typeof window.screenTop == "number") ? window.screenTop: window.screenY;
        
    
*   window.outerHight window.outerWidth 表示窗口的高和宽
*   window.innerHight window.innerWidth 表示页面视图的高和宽

打开新窗口

*   window.open(URL, target, args, is\_replace) URL: 新窗口打开的网址 target: 目标frame的名字，或者\_self, \_parent,\_top,_blank args: ![](http://wangsen.site/wp-content/uploads/2018/06/屏幕快照-2018-06-23-下午5.37.10.png)is_replace: 是否替代当前窗口
*   window.close() : 关闭一个窗口
*   window.closed: 布尔值，window是否已经关闭
*   window.opener: 表示打开本窗口的窗口window对象

超时调用与间歇调用

*   var id = setTimeout(function, ms)  ; clearTimeout(id);
*   var id = setInterval(function, ms) ; clearInterval(id);

系统对话框

*   alert('string')
*   var boolean = confirm("string");
*   var result\_string = prompt("string", "default\_answer");