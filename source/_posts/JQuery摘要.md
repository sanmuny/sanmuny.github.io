---
title: 'JQuery 摘要'
date: 2015-09-06 13:00:57
tags: [jquery]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
---

### 选择符与遍历

1.  $(): $函数接受css选择符作为参数，充当一个工厂函数，返回对应元素的JQuery对象。
    
2.  3种基本的选择符:
    
    *   标签名 $('p')
    *   ID $('#myid')
    *   类 $('.myClass')
3.  子元素组合符>: $('#myid > li')选择id为myid的元素的所有列表项(li).
    
4.  否定式伪类: $('#myid li:not(.myClass)')选择id为myid的元素中不属于myClass类的所有列表项(li).
    
5.  属性选择符$('img\[alt\]'): 选择所有带有alt属性的img元素.
    
6.  属性选择符+类正则匹配：
    
    *   $('a\[href^="mailto:"\]'): 选择所有URL以mailto:开头的超链接.
    *   $('a\[href$=".pdf"\]'): 选择所有URL以.pdf结尾的超链接.
    *   $('a\[href*="rose"\]'): 选择所有URL中包含rose的超链接.
7.  自定义选择符:
    
    *   $('li:eq(1)'): 选择第二个列表项
    *   $('li:odd'): 选择奇数的列表项
    *   $('li:even'): 选择偶数的列表项
    *   $('li:nth-child(odd)'): 选择从父元素的第一个元素开始计算的所有奇数列表项
    *   $('li:contain(string)'): 选择包含string的列表项
8.  表单选择符:
    
    *   :input
    *   :button
    *   :checked
    *   :selected
    *   :enabled
    *   :disabled
9.  filter: 过滤器，可接选择器和lambda函数
    
    *   $('li').filter(':even')
    *   $('li').filter(function(){return this.hostname && this.hostname!=location.hostname})
10.  next,nextALL,prev,prevAll,andSelf,siblings:选择所选元素的下一个元素等
    
11.  连缀(chaining)
    
    $('tr:contains(Henry)').parent().find('td:eq(1)').addClass('myClass').end().find('td:eq(2)').addClass('myClass');

12.  $('#myId').get(0) 等价于 $('myId')[0]
    

### 事件

1.  window.onload vs $(document).ready
    
    *   window.onload 是在页面的全部元素都下载好了之后才能执行，而$(document).ready()则是在DOM树准备好就可以执行了。
    *   window.onload只能指定一个函数，而$(document).ready()指定的所有函数都会按顺序执行。
    *   $(document).ready(func)可以简写为$(func);
2.  bind(event, func)函数可以为DOM节点绑定事件，以及事件发生时所执行的函数。event可以是：
    
    *   blur
    *   change
    *   click
    *   dbclick
    *   error
    *   focus
    *   keydown
    *   keypress
    *   keyup
    *   load
    *   mousedown
    *   mousemove
    *   mouseout
    *   mouseover
    *   mouseup
    *   resize
    *   scroll
    *   select
    *   submit
    *   unload
3.  toggle(func1, func2): 单击时轮流执行func1和func2
    
4.  toggleClass("someclass"): 单击时轮流添加或删除someclass.
    
5.  事件捕获和事件冒泡：事件捕获是从父节点开始将事件传递给子节点，而事件冒泡则正好相反。JQuery采取事件冒泡的策略。
    
6.  事件对象：事件发生时执行的函数可以把事件对象作为参数。
    
    *   event.target属性：保存发生事件的目标元素
    *   event.stopPropagation(): 阻止事件继续冒泡
    *   event.preventDefault(): 阻止事件的默认操作
7.  $(event.target).is(): 接收一个选择符表达式作为参数，并验证JQuery对象是否满足它。
    
8.  unbind(): 移除事件处理
    
9.  事件命名空间：bind('click.sometag', func) 可以在unbind的时候只解绑指定名字的事件。
    
10.  trigger(): 使用javascript去触发某个事件
    

### 效果

1.  .css(): 参数可以是("attr", "value")，也可以是({"attr": "value", "attr": "value"})，修改JQuery对象的css
2.  .hide(): 将JQuery对象的内联css属性"display"设置为"none"
3.  .show(): 将JQuery对象的内联css属性"display"恢复成hide之前的值。hide和show可以传入速度作为参数："slow", "normal", "fast"
4.  fadeIn()和fadeOut(): 淡入和淡出，可传入速度参数。
5.  slideDown()和slideUp(): 滑下和滑上，可传入速度参数。
6.  toggle(): 相当于轮流执行show()和hide()方法，可传入速度参数。
7.  slideToggle(): 相当于轮流执行slideUp()和slideDown()，可传入速度参数。
8.  animate(): 自定义动画。有两种传入参数的方式：
    *   ({"attr": "value", "attr", "value}, speed, easing, func):第一个参数是css属性，第二个是速度，第三个是缓动，第四个是动画完成后的回调函数。
    *   ({"attr": "value", "attr": "value"}, {duration: "value", easing: "value", complete: functiong(){}, queue: true...})
9.  outerWidth(true): 返回width+padding+border+margin的值
10.  outerWidth(), outerWidth(false): 返回width+padding+border的值
11.  innerWidth(): 返回width+padding的值
12.  outerHeight(), innerHeight()与outerWidth(), innerWidth()类似
13.  animate()中指定多个css属性变化可以让动画并发，而用多个效果方法如animate,fadeIn等连缀则可以让动画排队显示。设置queue参数为false则可以让该动画不用排队。
14.  css()方法不属于效果方法，不能排队，但可以用queue()方法将其加入队列，例如：
    *   .fadeTo().queue(function(next){$(xxx).css(); next();})
15.  JQuery为每个效果方法都提供了回调函数，可以用来让多个JQuery对象的动画排队执行。