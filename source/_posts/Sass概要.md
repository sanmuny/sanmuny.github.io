---
title: 'Sass 概要'
date: 2019-06-18 14:51:52
tags: [css,sass]
published: true
hideInList: false
feature: 
isTop: false
categories: 应用开发
---

前端开发中最大的坑之一莫过于写css，流水账式的写法让众多码农们头痛不已。好在有了sass，写css不再死板。sass对css的增强如下：

### 宏定义

宏定义的优点在于一处定义，多处使用，需要修改的时候只需要修改定义的地方即可。虽然sass没有明确的说明，但其实以下几种语法与C语言中的宏定义非常类似：

1.  变量 sass中的变量适用于替换css中参数的值。例如：
    
    scss:
    
    $basic-margin: "10px 20px";
    
    #box01 {
      margin: $basic-margin;
    }
    
    #box02 {
      margin: $basic-margin;
    }
    
    css:
    
    #box01 {
      margin: "10px 20px";
    }
    
    #box02 {
      margin: "10px 20px";
    }
    
    sass允许根据变量，选择性的输出css，类似于开关，例如：
    
    scss:
    
    $rounded-corners: false;
    
    .button {
      border: 1px solid black;
      border-radius: if($rounded-corners, 5px, null);
    }
    
    css:
    
    .button {
      border: 1px solid black;
    }
    
    sass 也允许在子模块中给变量设置默认值，引用的时候可以再重新定义变量的值
    
    _module.scss:
    
    $userColor: red !default;
    
    .bass {
      padding: 0 20px;
      color: $userColor;
    }
    
    test.scss:
    
    $userColor: black;
    @import "module";
    
    test.css:
    
    .bass {
      padding: 0 20px;
      color: black;
    }
    
    test.scss:
    
    @import "module";
    
    test.css
    .bass {
      padding: 0 20px;
      color: red;
    }
    
    使用!global可以在局部环境中设置全局变量的值，例如：
    
     test.scss:
    
    $color: red;
    
    .text {
      $color: black !global;
    }
    
    .box {
      color: $color;
    }
    
    test.css:
    
    .box {
      color: black;
    }
    
2.  mixin sass中的mixin类似于支持参数的代码片段，可以很方便的将一段常用的代码片段插入到css规则中去，例如：
    
    scss:
    
    @mixin normal-font($fontfamily) {
      font-size: 18px;
      font-family: $fontfamily;
    }
    
    .box {
      width: 200px;
      height: 200px;
      @include normal-font("IBM Plex Sans");
    }
    
    css:
    
    .box {
      width: 200px;
      height: 200px;
      font-size: 18px;
      font-family: "IBM Plex Sans";
    }
    
3.  扩展与继承 sass中的扩展相当于不带参数的代码片段，适用于同一组件的不同状态，语法如下：
    
    scss:
    
    %message-shared {
      border: 1px solid #ccc;
      padding: 10px;
      color: #333;
    }
    
    .message {
      @extend %message-shared;
    }
    
    .success {
      @extend %message-shared;
      border-color: green;
    }
    
    .error {
      @extend %message-shared;
      border-color: red;
    }
    
    .warning {
      @extend %message-shared;
      border-color: yellow;
    }
    
    css:
    
    .warning, .error, .success, .message {
      border: 1px solid #ccc;
      padding: 10px;
      color: #333;
    }
    
    .success {
      border-color: green;
    }
    
    .error {
      border-color: red;
    }
    
    .warning {
      border-color: yellow;
    }
    
     

### 模块化

sass也借鉴了编程语言中的模块化思想，允许文件引入。以下划线开头的文件类似于子模块，不会被被sass编译为css，只能被其他scss文件引用。例如：

 _module.scss:

.bass {
  padding: 0 20px;
}

test.scss:

@import "module";

$basic-margin: "10px 20px";

#box01 {
  margin: $basic-margin;
}

#box02 {
  margin: $basic-margin;
}

test.css:

.bass {
  padding: 0 20px;
}

#box01 {
  margin: "10px 20px";
}

#box02 {
  margin: "10px 20px";
}

### 语法简化

scss也对css的语法做了一些简化，比如说：

1.  嵌套 写scss子元素的规则不再另起一条规则，只需要嵌套在父元素中的规则中即可，例如：
    
     scss:
    
    $basic-margin: "10px 20px";
    
    .bss {
      margin: $basic-margin;
      #dash {
        margin-top: 20px;
        padding-top: 20px;
      }
      .dash {
        margin-top: 20px;
        margin-bottom: 20px;
      }
    }
    
    css:
    
    .bss {
      margin: "10px 20px";
    }
    .bss #dash {
      margin-top: 20px;
      padding-top: 20px;
    }
    .bss .dash {
      margin-top: 20px;
      margin-bottom: 20px;
    }
    
    另一种方式的嵌套：
    
     scss：
    
    .box {
      margin: {
        top: 20px;
        bottom: 10px;
        right: 10px;
        left: 20px;
      }
    }
    
    css:
    
    .box {
      margin-top: 20px;
      margin-bottom: 10px;
      margin-right: 10px;
      margin-left: 20px;
    }
    

### 可编程化

sass也做了一些工作让css更像一门编程语言而不是一遍作文。其中包括：

1.  支持运算 css是不支持运算的，而在scss中可以做一些简单的运算，例如：
    
     scss:
    
    .box {
      width: 100px / 200px * 100%;
    }
    
    css:
    
    .box {
      width: 50%;
    }
    
2.  数值类型 scss中的值分为以下几种类型：
    *   数字，例如： 20, 20px
    *   字符串，例如："IBM Plex Sans", bold
    *   颜色值，例如：#ffffff, blue
    *   布尔值, true, false
    *   列表，例如：0 20px 30px 40px
    *   字典，例如：("background": red, "foreground": pink)
3.  操作符 scss中的操作符包括：
    *   == , !=  : 判断两个值是否相等/不相等
    *   \+ \- \* / %
    *   < <= \> >=
    *   and or not
    *   \+ \- / 可用于字符串拼接
    *   () 用于优先级设定
    *   &父元素选择器
    *   #{} 可以将sass表达式插入到css的文本中
4.  代码注释
    *   // 单行注释，不会编译到css中
    *   /**/多行注释，一般会被编译进css
    *   压缩模式下，多行注释不会被编译进css，除非以/*!开头
    *   ///为文档注释，不会被编译到css中，会被sassdoc工具使用，生成sass的文档
5.  函数 scss中的函数主要用于数值计算，例如：
    
    scss:
    
    @function pow($base, $exponent) {
      $result: 1;
      @for $_ from 1 through $exponent {
        $result: $result * $base;
      }
      @return $result;
    }
    
    .sidebar {
      float: left;
      margin-left: pow(4, 3) * 1px;
    }
    
    css:
    
    .sidebar {
      float: left;
      margin-left: 64px;
    }
    
    Sass中的内建函数详见 [https://sass-lang.com/documentation/functions](https://sass-lang.com/documentation/functions)
6.   流程控制
    *   分支 @if , @else, @else if 例如：
        
        scss:
        
        @mixin triangle($color, $size, $direction) {
          display: block;
          height: 0;
          width: 0;
          border: $size/2 solid transparent;
        
          @if $direction == up {
            border-bottom-color: $color;
          } @else if $direction == down {
            border-top-color: $color;
          } @else if $direction == left {
            border-right-color: $color;
          } @else if $direction == right {
            border-left-color: $color;
          } @else {
            @error "wrong direction: #{$direction}";
          }
        }
        
        .next {
          @include triangle(green, 20px, right);
        }
        
        css:
        
        .next {
          display: block;
          height: 0;
          width: 0;
          border: 10px solid transparent;
          border-left-color: green;
        }
        
    *   循环 @each @for @while 例如：
        
         scss:
        
        $sizes: 40px, 50px, 80px;
        
        @each $size in $sizes {
          .icon-#{$size} {
            font-size: $size;
            height: $size;
            width: $size;
          }
        }
        
        css:
        
        .icon-40px {
          font-size: 40px;
          height: 40px;
          width: 40px;
        }
        
        .icon-50px {
          font-size: 50px;
          height: 50px;
          width: 50px;
        }
        
        .icon-80px {
          font-size: 80px;
          height: 80px;
          width: 80px;
        }
        
         scss:
        $base-color: #036;
        
        @for $i from 1 through 3 {
          ul:nth-child(3n + #{$i}) {
            background-color: lighten($base-color, $i * 5%);
          }
        }
        
        css:
        
        ul:nth-child(3n + 1) {
          background-color: #004080;
        }
        
        ul:nth-child(3n + 2) {
          background-color: #004d99;
        }
        
        ul:nth-child(3n + 3) {
          background-color: #0059b3;
        }
        
         scss:
        @function scale-below($value, $base, $ratio: 1.618) {
          @while $value > $base {
            $value: $value / $ratio;
          }
          @return $value;
        }
        
        $normal-font-size: 16px;
        sup {
          font-size: scale-below(20px, 16px);
        }
        
        css:
        sup {
          font-size: 12.36094px;
        }
        
7.  数值类型 sass中的数值类型包括以下几种：数值，字符串，颜色，List, Map，布尔值，null及函数
    *   数值包含数字和单位，sass的强大之处在于支持带单位的运算，例如：
        
        @debug 1in + 6px; // 102px or 1.0625in
        
    *   list和map的用法举例：
        
        $prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms);
        
        @function prefixes-for-browsers($browsers) {
          $prefixes: ();
          @each $browser in $browsers {
            $prefixes: append($prefixes, map-get($prefixes-by-browser, $browser));
          }
          @return $prefixes;
        }
        
        @debug prefixes-for-browsers("firefox" "ie"); // moz ms