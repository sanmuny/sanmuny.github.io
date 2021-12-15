---
title: 'HTML概要'
date: 2017-05-24 16:37:34
tags: [html]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
---

HTML CSS Javascript 的关系
-----------------------

HTML是网页内容的载体。内容就是网页制作者放在页面上想要让用户浏览的信息，可以包含文字、图片、视频等。

CSS样式是表现。就像网页的外衣。比如，标题字体、颜色变化，或为标题加入背景图片、边框等。所有这些用来改变内容外观的东西称之为表现。

JavaScript是用来实现网页上的动态效果。如:鼠标滑过弹出下拉菜单。或鼠标滑过表格的背景颜色改变。还有焦点新闻(新闻图片)的轮换。

HTML 标签语法
---------

1. **标签**由英文尖括号**<**和**>**括起来，如`<html>`就是一个标签。

2\. html中的标签一般都是成对出现的，分**开始标签**和**结束标签**。结束标签比开始标签多了一个/。

如：

（1） `<p></p>`

（2） `<div></div>`

（3）`<span></span>`

  
3\. 标签与标签之间是可以嵌套的，但先后顺序必须保持一致，如：`<div>`里嵌套`<p>`，那么`</p>`必须放在`</div>`的前面。如下图所示。

4\. HTML标签不区分大小写，`<h1>`和`<H1>`是一样的，但建议小写，因为大部分程序员都以小写为准。

HTML标签
------

**`<p>` 标签**

如果想在网页上显示文章，就需要<p>标签，把文章的段落放到<p>标签中。

语法：

`<p>段落文本</p>`

**`<hx\>` 标签**

使用`<hx>`标签来制作**文章的标题**。  
标题标签一共有6个，h1、h2、h3、h4、h5、h6分别为一级标题、二级标题、三级标题、四级标题、五级标题、六级标题。并且依据重要性递减。`<h1>`是最高的等级。

语法：

`<h1>段落文本</h1>`

**`<strong>` 和 `<em>`标签**

在一段话中特别强调某几个文字，这时候就可以用到`<em>`或`<strong>`标签。

但两者在强调的语气上有区别:`<em\>` 表示强调，`<strong>` 表示更强烈的强调。并且在浏览器中`<em\>` 默认用**斜体**表示，`<strong>` 用**粗体**表示。

语法：

`<em>段落文本</em>`

`<strong>段落文本</strong>`

**`<q>` 标签**

使用q标签可以在html中添加一段引用，如作家的话、诗句等。

1. 注意要引用的文本不用加**双引号**，浏览器会对q标签自动添加双引号。

语法：

段落文本`<q>`引用文本`</q>`段落文本

**`<blockquote\>` 标签**

blockquote的作用也是引用别人的文本。但它是对**长文本**的引用，如在文章中引入大段某知名作家的文字，这时需要这个标签。

1. 浏览器对`<blockquote>`标签的解析是**缩进**样式，而不是添加引号

语法：

`<blockquote>引用段落</blockquote>
`

**`<br  />` 标签**

 需要加回车换行的地方加入`<br />`，`<br />`标签作用相当于word文档中的回车。

1. 浏览器会忽略HTML中的回车和空格，如果需要换行，就要用到`<br  />`标签。

1. 如果需要加空格，则需要用`&nbsp`来替换空格。

语法：

引用段落`<br />`

**`<hr  /> `标签**

在信息展示时，有时会需要加一些用于分隔的横线，这样会使文章看起来整齐些.

1. `<hr />`标签和`<br />`标签一样也是一个**空标签**，所以只有一个开始标签，没有结束标签。

2. `<hr />`标签的在浏览器中的默认样式线条比较粗，颜色为灰色。可以通过css来改变水平线的样式。

语法：

`<p>文本段落</p>`

`<hr />`

**`<address>` 标签**

一般网页中会有一些网站的联系地址信息需要在网页中展示出来，这些联系地址信息如公司的地址就可以`<address>`标签。也可以定义一个地址（比如电子邮件地址）、签名或者文档的作者身份。

`<address>`标签中的内容会在浏览器中显示为斜体。

语法：

`<address>`地址信息或联系人信息`</address>`

![](http://wangsen.site/wp-content/uploads/2018/01/162315_bRJt_182939.png)

**`<code>`和`<pre>` 标签**

在介绍语言技术的网站中，避免不了在网页中显示一些计算机专业的编程代码，当代码为一行代码时，你就可以使用`<code>`标签了，如下面例子：

`<code>`var  i=i+300;`</code>`

如果是多行代码，可以使用`<pre>`标签。
`<pre>` 标签的主要作用:预格式化的文本。被包围在 pre 元素中的文本通常会保留空格和换行符。

语法：

`<code>一行计算机代码</code>
`
`<pre>多行计算机代码</pre>
`
**`<ul\>` 和 `<li>`标签**

利用`<ul>`和`<li>`可以生成没有顺序的列表。

语法：
```
<ul>
<li>[精彩少年](http://www.zuowen.com/e/20130805/51ff0eacd8d21.shtml)</li>
<li>[美丽突然出现](http://www.zuowen.com/e/20130802/51fb0a622ef8d.shtml)</li>
<li>[触动心灵的旋律](http://www.zuowen.com/e/20130713/51e0a742d81db.shtml)</li>
</ul>
```

**`<ol>` 和 `<li>`标签**

利用`<ol>`和`<li>`可以生成有顺序的列表。

语法：
```
<ol>

  <li>前端开发面试心法 </li>

  <li>[零基础学习](http://www.zuowen.com/e/20130802/51fb0a622ef8d.shtml)[html](http://www.zuowen.com/e/20130802/51fb0a622ef8d.shtml)</li>

  <li>JavaScript全攻略</li>

</ul>
```

**`<div>` 标签**

在网页制作过程过中，可以把一些独立的逻辑部分划分出来，放在一个`<div>`标签中，这个`<div>`标签的作用就相当于一个容器。

1. div和span类似，都没有特殊的语义。

1.  div可以用来排版。

2.  div属于块级元素。

3. 可以用id来为div分组命名

语法：

    <div  id=’xxx’>

    …

    </div>

**`<caption>` 标签**

可以使用caption标签来为表格添加标题：

语法：
```
<table>

<caption>标题文本</caption>

<tr>

 <td>…</td>

 <td>…</td>

 …

 </tr>

 …

</table>
```

**`<a>` 标签**

使用`<a>`标签可实现超链接，它在网页制作中可以说是无处不在，只要有链接的地方，就会有这个标签。

语法：

`<a href="目标网址" title="鼠标滑过显示的文本">链接显示的文本</a>
`
1\. title属性的作用：鼠标滑过链接文字时会显示这个属性的文本内容。这个属性在实际网页开发中作用很大，主要方便搜索引擎了解链接地址的内容（语义化更友好）
2\. `<a>`标签在默认情况下，链接的网页是在当前浏览器窗口中打开，如果需要在新的浏览器窗口中打开，则需要用到target选项。

`<a href="目标网址" **target="_blank"**>click here!</a>  
`
a标签还有一个作用是可以链接Email地址，使用mailto能让访问者便捷向网站管理者发送电子邮件。

**`<img>` 标签**

使用`<img>`标签可以在网页中插入图片。

语法：
`<img  src="图片地址" alt="下载失败时的替换文本" title = "提示文本">  
`
1、src：标识图像的位置；
2、alt：指定图像的描述性文本，当图像不可见时（下载不成功时），可看到该属性指定的文本；
3、title：提供在图像可见时对图像的描述(鼠标滑过图片时显示的文本)；
4、图像可以是GIF，PNG，JPEG格式的图像文件。

**HTML表单**
----------

表单可以把浏览者输入的数据传送到服务器端，这样服务器端程序就可以处理表单传过来的数据。

**语法：**
```
<form method="传送方式" action=”URL">

</form>
```
1. **<form>：**`<form>`标签是成对出现的，以`<form>`开始，以`</form>`结束。
2. **action：**浏览者输入的数据被传送到的地方,比如一个PHP页面(save.php)。
3. **method：** 数据传送的方式（get/post)
4. 所有表单控件（文本框、文本域、按钮、单选框、复选框等）都必须放在`<form></form>`标签之间* 
5. get请求会把表单提供的参数放到URL中，而post请求会把参数放到http请求体中

**文本、密码输入框**

当用户要在表单中键入字母、数字等内容时，就会用到**文本输入框**。文本框也可以转化为**密码输入框**。

**语法：**
```
<form>

<input type="text/password" name="名称" value="文本" />

</form>
```
1、type：

   当type="text"时，输入框为文本输入框;

   当type="password"时, 输入框为密码输入框。

2、name：为文本框命名，以备后台程序ASP 、PHP使用。

3、value：为文本输入框设置默认值。(一般起到提示作用)

**文本域 多行文本输入**

当用户需要在表单中输入大段文字时，需要用到文本输入域。

**语法：**

    <textarea  **rows="行数"**  **cols="列数"**>

    文本

    </textarea>

1、`<textarea>`标签是成对出现的，以`<textarea>`开始，以`</textarea>`结束。

2、**cols**：多行输入域的**列数**。

3、**rows**：多行输入域的**行数**。

4、在`<textarea></textarea>`标签之间可以输入**默认值**。

**单选框、复选框**

在使用表单设计调查表时，为了减少用户的操作，使用选择框是一个好主意，html中有两种选择框，即**单选框**和**复选框**，两者的区别是**单选框**中的选项用户只能选择一项，而**复选框**中用户可以任意选择多项，甚至全选

**语法：**

`<input type="radio/checkbox" value="值" name="名称" checked="checked"/>
`
1、type:

   当 type="radio" 时，控件为单选框

   当 type="checkbox" 时，控件为复选框

2、value：提交数据到服务器的值（后台程序PHP使用）

3、name：为控件命名，以备后台程序 ASP、PHP 使用

4、checked：当设置 checked="checked" 时，该选项被默认选中

**下拉列表框**

下拉列表在网页中也常会用到，它可以有效的节省网页空间。既可以单选、又可以多选

下拉列表也可以进行多选操作，在`<select>`标签中设置multiple="multiple"属性，就可以实现多选功能

**提交按钮**

在表单中有两种按钮可以使用，分别为：提交按钮、重置。这一小节讲解提交按钮：当用户需要提交表单信息到服务器时，需要用到**提交按钮**。

**语法**：

`<input type="submit" value="提交">
`
1, type：只有当type值设置为submit时，按钮才有提交作用
2, value：按钮上显示的文字

**重置按钮**

当用户需要重置表单信息到初始时的状态时，比如用户输入“用户名”后，发现书写有误，可以使用重置按钮使输入框恢复到初始状态。只需要把type设置为"reset"就可以。

**语法**：

`<input type="reset"  value="重置">
`
1, type：只有当type值设置为reset时，按钮才有重置作用

2, value：按钮上显示的文字


**form表单中的label标签**

label标签不会向用户呈现任何特殊效果，它的作用是为鼠标用户改进了可用性。如果你在 label 标签内点击文本，就会触发此控件。

**语法**：

    <label  for="控件id名称"> </label>


**HTML5**
---------

**更简化的语法：**

*   `<!doctype html>`
*   `<html  lang=“zh-CN”>`
*   `<meta  charset=“utf-8”>`
*   不区分大小写 HTML  =  HtMl
*   checked=“checked” or checked
*   `<html><head><body>`可以省略

      。。。

**废除的标签**

*   能用css替代的标签：basefont,  font,  center…
*   Frame相关的标签，只支持iframe： frameset,  frame,  noframes
*   只有部分浏览器支持的标签：applet,  bgsound…
*   其他废除的标签：rb,  dir,  listing,  xmp…

**废除了一些和样式相关的属性**

**HTML5 新增标签**

*   `<section></section>` 表示页面中的一个内容区块，不用作排版
*   `<article></article>` 表示与上下文不相干的独立内容，比如说文章
*   `<aside></aside>` 表示与文章相关的辅助信息，如文章后面的推荐阅读
*   `<header></header>` 页眉
*   `<footer></footer>` 页脚
*   `<nav></nav> `导航栏
*   `<figure></figure> ` 标签规定独立的流内容（图像、图表、照片、代码等等）。figure 元素的内容应该与主内容相关，但如果被删除，则不应对文档流产生影响。
*   `<figcaption></figcaption>`标签定义 figure 的标题（caption）。"figcaption" 元素应该被置于 "figure" 元素的第一个或最后一个子元素的位置。
*   `<video></video>` 视频标签
*   `<audio></audio> `音频标签
*   `<embed  /> `嵌入标签，可以是视频、音频、flash等多媒体内容
*   `<mark></mark>` 替换文本的背景色来标记文本
*   <`progress  max=“100”  value=“85”></progress>` 进度条
*   `<canvas></canvas>` 绘图，替代flash
*   `<details><summary>always  show</summary>something  can  be  hidden</details>`
*  ` <datalist></datalist>`

**HTML5** **新增属性**

*   `<html  manifest=“xxx.manifest”>`
*   `<meta  charset=“utf-8”>`
*   `<link  rel=‘icon’  href=“URL”  type=image/gif  sizes=“16x16”> `//目前几乎没有主流浏览器支持 sizes 属性。
*   `<script  defer  src=“*.js”></script>`
*   `<script  async  src=“*.js”></script>`
*   `<a  media=“tv”  href=[“www.baidu.com](http://www.baidu.com/)”  hreflang=“zh”  ref=“external”>baidu</a>`
*   `<ol  start=”50”  reversed></ol>`
*   `<style  scoped></style>`
*   `<iframe  seamless  srcdoc=“<h1>hello</h1>”  sandbox  src=[www.baidu.com](http://www.baidu.com/)>  </iframe>`

**HTML5** **新增全局属性**

*   data-*
*   Hidden
*   Tabindex
*   spellcheck=”true”
*   contenteditable=“true”