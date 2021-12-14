---
title: '使用jQuery Validation插件来验证表单'
date: 2016-04-01 12:06:43
tags: [jquery]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
---

[jQuery Validation](http://jqueryvalidation.org/)是一个用于验证表单的jQuery插件，简单易用，已经包含了16种内置的验证规则．[Github](https://github.com/jzaefferer/jquery-validation/tree/master/src/additional)上也有更多的验证规则可以使用．这都不是重点，重点是你可以轻松的定制自己的规则．

内置规则：

*   required – Makes the element required.
*   remote – Requests a resource to check the element for validity.
*   minlength – Makes the element require a given minimum length.
*   maxlength – Makes the element require a given maximum length.
*   rangelength – Makes the element require a given value range.
*   min – Makes the element require a given minimum.
*   max – Makes the element require a given maximum.
*   range – Makes the element require a given value range.
*   step – Makes the element require a given step.
*   email – Makes the element require a valid email
*   url – Makes the element require a valid url
*   date – Makes the element require a date.
*   dateISO – Makes the element require an ISO date.
*   number – Makes the element require a decimal number.
*   digits – Makes the element require digits only.
*   equalTo – Requires the element to be the same as another one

**_内置规则的使用_**

内置规则的使用非常简单：

首先将该插件的js文件包含进html文件：
```
    <script src="/static/js/jquery.min.js"></script>
    <script src="/static/js/jquery.validate.min.js"></script>
```

然后用jQuery选择需要验证的表单，执行validate()函数即可：
```
    <script>
    $("#form_id").validate();
    </script>
 ```   

jQuery Validation官网上的例子：
```
        <!DOCTYPE HTML>
        <html>
        <head>
        <script src="jquery.min.js"></script>
        </head>
        <body>
        <form class="cmxform" id="commentForm" method="get" action="">
        <fieldset>
        <legend>Please provide your name, email address (won't be published) and a comment</legend>
        <p>
        <label for="cname">Name (required, at least 2 characters)</label>
        <input id="cname" name="name" minlength="2" type="text" required>
        </p>
        <p>
        <label for="cemail">E-Mail (required)</label>
        <input id="cemail" type="email" name="email" required>
        </p>
        <p>
        <label for="curl">URL (optional)</label>
        <input id="curl" type="url" name="url">
        </p>
        <p>
        <label for="ccomment">Your comment (required)</label>
        <textarea id="ccomment" name="comment" required></textarea>
        </p>
        <p>
        <input class="submit" type="submit" value="Submit">
        </p>
        </fieldset>
        </form>
        <script>
        $("#commentForm").validate();
        </script>
        </body>
        </html>
 ```   

jQuery Validation会根据表单设置的type和属性自动为他们分配内置的规则，比如email,url,required等．

运行一下看看：

什么都不输入，直接点提交

输入错误的Email地址，改正后错误提示自动消失

**_添加自定义规则_**

jQuery Validation最吸引人的feature，它可以轻松的加入自定义的规则：

第一步，在js中调用jQuery.validator.addMethod函数来添加规则，例如添加IP格式检查的规则：

    $.validator.addMethod( "ipv4", function( value, element ) {return this.optional( element ) || /^(25[0-5]|2[0-4]\d|[01]?\d\d?)\.(25[0-5]|2[0-4]\d|[01]?\d\d?)\.(25[0-5]|2[0-4]\d|[01]?\d\d?)\.(25[0-5]|2[0-4]\d|[01]?\d\d?)$/i.test( value );}, "Invalid IP v4 address." );　//自定义其他规则只需要替换规则名"ipv4",正则表达式//之间的内容,以及出错后显示的字符串"Invalid IP v4 address."即可．
    

第二步，把规则应用到指定的表单项，即在执行$("#form_id").validate()函数的时候加入rules参数：
```
        23 $("#ip_form").validate({
        24     rules: {
        25         ip: {
        26             required: true,
        27             ipv4: true,
        28         },
        29         netmask: {
        30             required: true,
        31             ipv4: true,
        32         },
        33         gateway: {
        34             required: true,
        35             ipv4: true,
        36         },
        37     },
        38 }); //其中ip,netmask,gateway为表单项的name属性．required和ipv4是规则名．
```    

生效后的样子，可以添加如下css来修改错误信息的样式：
```
        label.error {
            margin-left: 10px;
            padding-left: 5px;
            padding-right: 5px;
            color: #E2303A;
            font-style: italic;
            font: Helvetica Neue, 12px, #E2303A;
            border: 1px solid #F2A9AE;
        }
```  

**_使用json提交数据_**

表单验证通过后，提交动作默认是使用form本身的提交动作，即指定form的action和method属性：

` method="get" action=""`

可以在validate()函数中添加submitHandler参数来指定点击提交后执行的函数，我们可以在该函数中使用$.json来提交数据：
```
        23 $("#ip_form").validate({
        24     rules: {
        25         ip: {
        26             required: true,
        27             ipv4: true,
        28         },
        29         netmask: {
        30             required: true,
        31             ipv4: true,
        32         },
        33         gateway: {
        34             required: true,
        35             ipv4: true,
        36         },
        37         dns: {
        38             dns: true,
        39         }
        40     },
        41     submitHandler: function(form) {
        42         ip_ok();
        43         $("#ip-conf").modal('hide');
        44     }
        45 });
```    