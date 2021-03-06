---
title: 'CSS概要'
date: 2017-07-05 14:09:26
tags: [css]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
---

#### 语法
CSS全称为“层叠样式表 (Cascading Style Sheets)”，它主要是用于定义HTML内容在浏览器内的显示样式， 如文字大小、颜色、字体加粗等。使用CSS样式的一个好处是通过定义某个样式，可以让不同网页位置的 文字有着统一的字体、字号或者颜色等。

CSS语法  
    选择符 { 属性:值}

*   选择符:又称选择器，指明网页中要应用样式规则的元素，如本例中是网页中所有的段(p)的文字将变 成蓝色，而其他的元素(如ol)不会受到影响。
*   声明:在英文大括号“{}”中的的就是声明，属性和值之间用英文冒号“:”分隔。当有多条声明时，中间 可以英文分号“;”分隔

1.  最后一条声明可以没有分号，但是为了以后修改方便，一般也加上分号
2.  CSS注释 - /*注释语句*/
3.  CSS的某些样式是具有继承性的。
4.  为了使用样式更加容易阅读，可以将每条代码写在一个新行内

#### 插入方式
CSS样式可以写在哪些地方呢?从CSS 样式代码插入的形式来看基本可以分为以下3种:内联式、嵌入式和
外部式三种。

1.  内联式:把css代码用style属性直接写在现有的HTML标签中。如: `<p style="color:red">这里文字是红色。</p>`
2.  嵌入式:把css样式代码写在`<style type=“text/css”></style>`标签之间。并且一般情况下嵌入式css样式 写在`<head></head>`之间。如:`<style type="text/css"> span{ color:red; } </style>`
3.  外部式:把css代码写一个单独的外部文件中，这个css样式文件以“.css”为扩展名，在`<head>`内(不是在`<style>`标签内)使用`<link>`标签将css样式文件链接到HTML文件内。如: `<link href="base.css" rel="stylesheet" type="text/css" />`

优先级:内联式 > 嵌入式 > 外部式 ?

离被设置元素越近优先级别越高

#### 权值、层叠、重要性
标签的权值为1，类选择符的权值为10，ID选择符的权值最高为100

*   `p{color:red;}` /*权值为1*/
*   `p span{color:green;}` /*权值为1+1=2*/
*   `.warning{color:white;} `/*权值为10*/
*   `p span.warning{color:purple;}` /*权值为1+1+10=12*/
*   `#footer .note p{color:yellow;}` /*权值为100+10+1=111*/

层叠就是在html文件中对于同一个元素可以有多个css样式存在，当有相同权重的样式存在时，会根据这些 css样式的前后顺序来决定，处于最后面的css样式会被应用。
``` css
p{color:red;}  
p{color:green;}  
<p class="first">hello world</p>
```
!important语句可以把样式改变为最高权值
``` css
p{color:red !important;} p{color:green;}  
<p class="first">hello world</p>
```
#### 单位和值

颜色值

1.  英文命令颜色。 如: `p{color:red;}`
2.  RGB颜色 `p{color:rgb(133,45,200);} `
3.  3.十六进制颜色 `p{color:#00ffff;}`

长度值

1.  像素。 如: `p{font-size: 20px}`
2.  em。1 em = 父元素的font-size 如: `p{font-size:14px} span{font-size:0.8em;} <p>以这个<span>例子</span>为例。</p>`
3.  百分比` p{font-size:12px;line-height:130%}`

#### CSS 选择器

在{}之前的部分就是“选择器”，“选择器”指明了{}中的“样式”的作用对象，也就是“样式”作用于网页中的哪些元素。

*   标签选择器 - 标签选择器其实就是html代码中的标签。
    
*   类选择器
    
    .setGreen {  
        color:green;  
    }
    
    <span class=”setGreen">胆小如鼠</span>
    
*   ID选择器
    
    #setGreen {  
         color:green;  
    }
    
*   子选择器 - 即大于符号(>),用于选择指定标签元素的**第一代**子元素
    
    .food>li{border:1px solid red;}
    
*   包含选择器 - 即加入空格,用于选择指定标签元素下的后辈元素
    
*   通用选择器 - 它使用一个(*)号指定，它的作用是匹配html中所有标签元素
    
*   伪类选择符 - 它允许给html不存在的标签(标签的某种状态)设置样式，如:`a:hover{color:red;}`
    
*   分组选择符 - html中多个标签元素设置同一个样式时，可以使用分组选择符
    
    h1,span{color:red;}

#### CSS 排版 

1.  设置字体: `font-family:"宋体”`
    
2.  设置字号、颜色:`font-size:12px;color:#666`
    
3.  设置文字的样式:粗体、斜体、下划线、删除线:
    
    *   `font-weight:bold`
        
    *   `font-style:italic`
        
    *   `text-decoration:underline;`
        
    *   `text-decoration:line-through;`
        
4.  设置文字缩进:`text-indent:2em;`
    
5.  设置行高:`line-height:2em;`
    
6.  设置字符间距、单词间距:
    
    *   `letter-spacing:50px;`
        
    *   `word-spacing:50px;`
        
7.  设置对齐方式:
    
    *   `text-align:left;`
        
    *   `text-align:center;`
        
    *  ` text-align:right;`
        

#### CSS 布局模型
元素分类 在CSS中，html中的标签元素大体被分为三种不同的类型:块状元素、内联元素(又叫行内元素)和内联块状元素。

常用的块状元素(display: block)有: `<div>、<p>、<h1>...<h6>、<ol>、<ul>、<dl>、<table>、<address>、<blockquote> 、<form>`

*   每个块级元素都从新的一行开始，并且其后的元素也另起一行。
*   元素的高度、宽度、行高以及顶和底边距都可设置。
*   元素宽度在不设置的情况下，是它本身父容器的100%(和父元素的宽度一致)，除非设定一个宽度。

常用的内联元素(display: inline)有: `<a>、<span>、<br>、<i>、<em>、<strong>、<label>、<q>、<var>、<cite>、<code>`

*   和其他元素都在一行上;
*   元素的高度、宽度及顶部和底部边距不可设置;
*   元素的宽度就是它包含的文字或图片的宽度，不可改变。

常用的内联块状元素(display: inline-block)有: `<img>、<input>`

*   和其他元素都在一行上;
*   元素的高度、宽度、行高以及顶和底边距都可设置。

#### 流动模型 flow

流动模型，流动(Flow)是默认的网页布局模式。也就是说网页在默认状态下的 HTML 网页元素都是根据流动模型来分布网页内容的。 流动布局模型具有2个比较典型的特征:

*   块状元素都会在所处的包含元素内自上而下按顺序垂直延伸分布，因为在默认状态下，块状元素的 宽度都为100%。实际上，块状元素都会以行的形式占据位置。
*   在流动模型下，内联元素都会在所处的包含元素内从左到右水平分布显示。

#### 浮动模型 float
浮动模型，浮动(Float)如果想让两个块状元素并排显示，需要用到浮动模型。
``` css
div {  
    width:200px;  
    height:200px;  
    border:2px red solid;  
    float:left;  
}

<div id="div1"> </div>  
<div id="div2"> </div>
```

#### 层模型

什么是层布局模型?层布局模型就像是图像软件PhotoShop中非常流行的图层编辑功能一样，每个图层能够 精确定位操作，但在网页设计领域，由于网页大小的活动性，层布局没能受到热捧。但是在网页上局部使用 层布局还是有其方便之处的

层模型有三种形式:

1. 绝对定位(position: absolute)
2. 相对定位(position: relative)
3. 固定定位(position: fixed)

如果想为元素设置层模型中的**绝对定位**，需要设置`position:absolute`(表示绝对定位)，这条语句的作用将元素从文档流中拖出来，然后使用left、right、top、bottom属性相对于其最接近的一个具有定位属性的父包含块 进行绝对定位。如果不存在这样的包含块，则相对于body元素，即相对于浏览器窗口。

如果想为元素设置层模型中的**相对定位**，需要设置`position:relative`(表示相对定位)，它通过left、right、top、 bottom属性确定元素在正常文档流中的偏移位置。相对定位完成的过程是首先按static(float)方式生成一个元 素(并且元素像层一样浮动了起来)，然后相对于以前的位置移动，移动的方向和幅度由left、right、top、 bottom属性确定，偏移前的位置保留不动。

fixed:表示**固定定位**，与absolute定位类型类似，但它的相对移动的坐标是视图(屏幕内的网页窗口)本身。 由于视图本身是固定的，它不会随浏览器窗口的滚动条滚动而变化，除非你在屏幕中移动浏览器窗口的屏幕 位置，或改变浏览器窗口的显示大小，因此固定定位的元素会始终位于浏览器窗口内视图的某个位置，不会 受文档流动影响

#### 弹性盒布局

详细介绍请参考：[https://www.ibm.com/developerworks/cn/web/1409\_chengfu\_css3flexbox/](https://www.ibm.com/developerworks/cn/web/1409_chengfu_css3flexbox/)

``` css
.flex-container {
list-style: none;
display: flex;
flex-direction: row;
flex-wrap: wrap;
}

<ul class="flex-container">
<li class="flex-item"><imgsrc="//placehold.it/300&text=1"></li>
<li class="flex-item"><img src="//placehold.it/300&text=2"></li>
<li class="flex-item"><img src="//placehold.it/300&text=3"></li>
<li class="flex-item"><img src="//placehold.it/300&text=4"></li>
<li class="flex-item"><img src="//placehold.it/300&text=5"></li>
<li class="flex-item"><img src="//placehold.it/300&text=6"></li>
</ul>

.flex-item {   
    padding: 5px;
}
```

#### CSS 设计技巧

• 水平居中设置-行内元素

通过给父元素设置 `text-align:center `来实现的
  
• 水平居中设置-定宽块状元素

通过设置“左右margin”值为“auto”来实现居中的

• 水平居中设置-不定宽块状元素方法

1.  加入 table 标签  
2.  设置 display: inline 方法:与第一种类似，显示类型设为 行内元素，进行不定宽元素的属性设置  
3.  设置 position:relative 和 left:50%:利用 相对定位 的方式，将元素向左偏移 50% ，即达到居中的目的
   
• 垂直居中-父元素高度确定的单行文本

通过设置父元素的 height 和 line-height 高度一致来实现的

• 垂直居中-父元素高度确定的多行文本

1.  使用插入 table (包括tbody、tr、td)标签，同时设置 vertical-align:middle
2.  设置块级元素的 display 为 table-cell(设置为表格单元显示)，激活 vertical-align 属性