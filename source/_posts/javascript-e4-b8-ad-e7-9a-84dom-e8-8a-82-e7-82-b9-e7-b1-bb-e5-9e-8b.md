---
title: 'Javascript中的DOM节点类型'
date: 2018-07-11 13:49:39
tags: [DOM,javascript]
published: true
hideInList: false
feature: 
isTop: false
---

### Node类型

*   node.nodeType:
    *   Node.ELEMENT_NODE(1);
    *   Node.ATTRIBUTE_NODE(2);
    *   Node.TEXT_NODE(3);
    *   Node.CDATA\_SECTION\_NODE(4);
    *   Node.ENTITY\_PREFERENCE\_NODE(5);
    *   Node.ENTITY_NODE(6);
    *   Node.PROCESSING\_INSTRUCTION\_NODE(7);
    *   Node.COMMENT_NODE(8);
    *   Node.DOCUMENT_NODE(9);
    *   Node.DOCUMENT\_TYPE\_NODE(10);
    *   Node.DOCUMENT\_FRAGMENT\_NODE(11);
    *   Node.NOTATION_NODE(12);
*   node.nodeName: 标签名
*   node.childNodes\[0\]  node.childNodes.item(0);
*   node.childNodes.length;
*   node.parentNode;
*   node.nextSibling;
*   node.previousSibling;
*   node.firstChild;
*   node.lastChild;
*   node.appendChild(newNode);
*   node.insertBefore(newNode);
*   node.replaceChild(newNode, oldNode);
*   node.removeChild(node);
*   node.cloneNode(true//深复制)

### HTMLDocument类型

*   document.nodeType = 9
*   document.nodeName = "#document"
*   document.nodeValue = null
*   document.parentNode = null
*   document.ownerDocument = null
*   document.firstChild === document.childNodes\[0\] === document.documentElement 都指向<html>元素
*   document.body指向<body>元素
*   document.head
*   document.charset
*   document.URL;
*   document.title;
*   document.domain;
*   document.getElementById();
*   document.getElementByClassName();
*   document.getElementsByTagName();
*   document.getElementsByName();
*   document.images;
*   document.anchors;
*   document.links;
*   document.forms;
*   document.createElement('div')
*   document.activeElement: 指向获得焦点的元素
*   document.readyState // loading or complete

### Element类型

var elem = document.getElementById('elem_id');

*   elem.nodeType = 1
*   elem.nodeName 为元素的标签名
*   elem.nodeValue = null
*   elem.parentNode 为document或者Element
*   elem.nodeName === elem.tagName
*   elem.getAttribute("id")
*   elem.setAttribute("id", "new_id")
*   elem.attributes.getNamedItem('id')
*   elem.attributes.removeNamedItem('id')
*   elem.attributes.setNamedItem(new_attr)
*   elem.getElementsByTagName()
*   elem.dataset // data- 前缀自定义的属性及属性值
*   elem.innerHTML // 元素的内容（所有子节点HTML）
*   elem.scrollIntoView() //将元素滚动至可见视图中
*   elem.style.width
*   elem.style

### Text类型

<div>hello world</dev> var text\_node = div.firstChild; var text\_node = document.createTextNode(text);

*   text\_node.nodeValue = text\_node.data = 'hello world'
*   text_node.appendData('string');
*   text_node.deleteData(offset, count);
*   text_node.insertData(offset, text);
*   text\_node.replaceData(offset, count, new\_text);
*   text_node.splitText(offset);
*   text_node.substringData(offset);
*   text\_node.length = text\_node.data.length = text_node.nodeValue.length;
*   text_node.parentNode.normalize() // 将两个子文本节点合并

### Comment类型

    <div id='mydiv'><!-- comment --></div> var mydiv = document.getElementById('mydiv'); var comment = mydiv.firstChild; var comment = document.createComment('comment');

*   comment类型和Text类型继承自同一个基类，拥有除splitText之外Text的所有属性和方法

### Attr类型

elem.attributes中的节点 var attr = document.createAttribute(attr_name);

*   attr.nodeType = 11
*   attr.nodeName 属性名
*   attr.nodeValue 属性值
*   attr.parentNode = null
*   elem.setAttributeNode(attr);