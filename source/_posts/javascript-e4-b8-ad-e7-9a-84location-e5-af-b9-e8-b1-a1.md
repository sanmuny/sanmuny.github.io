---
title: 'Javascript中的location对象'
date: 2018-06-25 15:59:51
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

*   window.location === document.location // true
*   location的属性列表
*   修改location的属性会导致页面刷新并产生新的历史记录
*   window.location = "URL" 和 location.href="URL"效果一样，都会调用location.assign(URL)
*   location.replace(URL)不会产生新的历史记录，而且会禁用后退
*   location.reload(true): 重新加载页面，参数可以选择是否从服务器加载（而不是从缓存加载）