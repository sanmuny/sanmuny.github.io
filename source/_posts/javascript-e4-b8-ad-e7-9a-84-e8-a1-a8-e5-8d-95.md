---
title: 'Javascript中的表单'
date: 2018-07-18 13:57:47
tags: [javascript]
published: true
hideInList: false
feature: 
isTop: false
---

var form = document.getElementById('myform');

*   form.acceptCharset 服务器能处理的字符集
*   form.action 接受请求的URL
*   form.elements 表单中的所有控件
*   form.enctype 编码类型
*   form.length 控件数量
*   form.method HTTP请求类型
*   form.name   // document.forms\[form.name\]
*   form.reset()
*   form.submit()
*   form.target

var text = form.elements\['sex'\];

*   text.disabled
*   text.form
*   text.name
*   text.value
*   text.readOnly
*   text.tabIndex
*   text.type
*   text.select()
*   text.setSelectionRange(start, end)
*   text.value.substring(text.selectionStart, text.selectionEnd)

text的事件：

*   change
*   focus
*   blur

剪贴板 var clipboard = window.clipboardData ; //event.clipboardData;

*   clipboard.getData()
*   clipboard.setData()
*   clipboard.clearData()

剪贴板事件

*   beforecopy
*   copy
*   beforecut
*   cut
*   beforepaste
*   paste