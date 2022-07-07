---
title: Go语言中的正则表达式
date: 2022-01-12 17:50:04
tags:
  - go
  - regexp
categories:
  - 计算机基础
cover: /images/go.png
---

正则表达式在字符串的处理中占有重要的地位，Go语言中的regexp包提供了对正则表达式的支持。

### 正则表达式基础

正则表达式(Regular Expression)是一个特殊的字符串，它定义了一种文本模式。通过正则表达式，你可以从所有文本中匹配到满足特定模式的文本(字符串)，然后可以：

1. 测试字符串是否满足这种特定模式，例如：是不是IP地址、电话号码，银行卡号等
2. 替换文本，将文本中部分或者所有满足这种特定模式的字符串替换、删除掉
3. 提取满足这种特定模式的子字符串

#### 元字符
元字符在正则表达式中有特殊的意义，要匹配这些元字符本身的话，需要用反斜杆转义。元字符有12个，如下所示：

```
$()*+.?[\^{|
```

`]`和`}`不属于元字符是因为它们是否需要被转义依赖于前面是否有`[`和`{`

#### 量词

以下几个字符我们称之为量词，它们表示匹配字符重复的次数。

| 量词 | 含义 |
| --- | --- |
| ? | 重复0次或者1次 |
| * | 重复0次或者多次 |
| + | 重复1次或者多次 |
| {m, n} | m,n为数字，至少重复m次，最多重复n次, m和n中的一个可以不指定 |

#### 惰性匹配和贪婪匹配

在量词后面加`?`表示惰性匹配，如`*?`,`+?`,`??`,`{m,n}?`等。惰性匹配每匹配到一次就会停下来然后接着匹配表达式后面的部分，如果后面的部分匹配成功，则停止匹配。

贪婪匹配则相反，量词匹配成功后不会停下来，它会接着匹配直到失败，然后回溯之后再接着匹配表达式后面的部分。

例如下面的HTML文本，

```
<p>
The very <em>first</em> task is to find the beginning of a paragraph.
</p>
<p>
Then you have to find the end of the paragraph
</p>
```

如果用正则表达式`<p>.*?</p>`来惰性匹配，它只匹配到第一个段，而如果用正则表达式`<p>.*</p>`来进行贪婪匹配，它会匹配到整个文本。

#### 匹配多个备选中的一个

```
Mary|Jane|Sue
```
匹配 Mary, 或者 Jane 或者 Sue.


#### 零宽断言(Zero-Length Assertions) 或 环视 (lookaround)

零宽断言用来匹配某个字符串之前或者之后的文本，但匹配到的结果不包含该字符串本身。因为不包含该字符串，所以该断言匹配到的文本长度为0，所以称之为零宽断言。

零宽断言分为两种：

1. 回望(lookbehind)，即从匹配位置往后(左)查询。表达式为 `(?<=exp)`，意思是如果文本的左边满足正则表达式exp，则匹配该文本，不包含exp本身匹配到的字符串。
2. 前瞻(lookahead)，即从匹配位置往前(右)查询。表达式为`(?=exp)`，意思是如果文本的右边满足正则表达式exp，则匹配该文本，不包含exp本身匹配到的字符串。

负向零宽断言(Negative lookaround)则是匹配不满足exp的情形，对应到回望和前瞻则是 `(?<!=exp)` 和 `(?!=exp)`。例如`(?<!=exp)`意思是如果文本的左边**不**满足正则表达式exp，则匹配该文本，同样不包含exp本身匹配不成功的字符串。

下面的例子可以说明零宽断言的作用。

如果需要匹配HTML中加粗的文本，但不包含HTML标签本身则可以用下面的正则表达式：

```
(?<=<b>)\w+(?=</b>)
```

如果用它来匹配文本 `My <b>cat</b> is furry` 的话，只会匹配到`cat`，`<b>`和`</b>`不会被匹配到。


#### 字符类
正则表达式内置了一些字符类，通过下表所列的字符类的写法可以匹配到多个属于这一类字符。

|字符类|含义|
|---|---|
|[chars]|匹配chars中的任一字符|
|[^chars]|匹配任一不在chars中的字符|
|[:name:]|字符类中的所有ASCII字符，name为分类名，正则表达式支持的类名及含义如下表所示|
|[^:name:]|不属于字符类中的所有ASCII字符|
|[[:name:]]|字符类中的任一ASCII字符|
|[[^:name:]]|不在字符类中的任一ASCII字符|
|.|匹配任一字符，如果不指定s修饰符的话，不包括换行符|
|\d|匹配任一ASCII数字，相当于[0-9]|
|\D|匹配任一非数字ASCII字符，相当于[^0-9]|
|\s|空白字符，[ \t\v\n\f\r]|
|\S|非空白字符|
|\w|任一ASCII单词字符，相当于[0-9A-Za-z_]|
|\W|任一ASCII非单词字符，[^0-9A-Za-z_]|
|\p{name}|匹配unicode某一类中的字符|
|\P{name}|匹配不在unicode某一类中的字符|

#### 分组与捕获

可以用 `()`对正则表达式进行分组，例如：

```
\bMary|Jane|Sue\b
```
表示 `\bMary`，`Jane`，`Sue\b`中的一个，这显然不是我们想要的。这时候就需要用括号进行分组。

```
\b(Mary|Jane|Sue)\b
```

上面的正则表达式虽然达到了我们想要的结果，但是也捕获了一个分组。()中匹配到的内容会被记录到分组1里面。假如后面还有多个()，则分组名依次加1。但显然我们这里并没有用到捕获的内容，所以可以用下面的表达式告诉引擎不要捕获。

```
\b(?:Mary|Jane|Sue)\b
```

那如果我们想要使用捕获到的结果该怎么办呢？可以用 \1 来引用它。 1位分组名，如果要引用第二个分组捕获到的内容则是 \2 (Go语言不支持)

```
\b\d\d(\d\d)-\1-\1\b
```
上面的表达式可以匹配 `2008-08-08`, `2009-09-09`这样的日期。

如果不想使用默认的数字捕获组名字，可以用下面的方法来给捕获组命名。

```
\b\d\d(?P<m>\d\d)-\k<m>-\k<m>\b
```

#### 正则表达式的选项

| 选项 | 含义 |
| --- | --- |
| i | 大小写不敏感 |
| m | 多行模式 |
| s | . 可以匹配新行 |

### Go语言中的regexp包

#### regexp中的函数

| 函数 | 作用 |
| --- | --- |
| regexp.Compile(exp) | 用exp编译一个正则表达式，如果成功则返回 *regexp.Regexp和nil |
| regexp.MustCompile(exp) | 同上，但编译失败则会触发panic |
| regexp.Match(exp, b) | 如果b []byte 满足exp，则返回true, nil |
| regexp.MatchReader(exp, r) | 同上, r为 io.RuneReader类型 |
| regexp.MatchString(exp, s) | 同上，s为字符串类型 |

#### Regexp类型(*regexp.Regexp)的方法

| 方法 | 作用 |
| --- | --- |
| rx.Match(b)| 如果类型为[]byte的b满足正则表达式，则返回true |
| rx.MatchReader(r) | 如果类型为io.RuneReader的r满足正则表达式，则返回true |
| rx.MatchString(s) | 如果字符串s满足正则表达式，则返回true |
| rx.ReplaceAllString(s, sr) | 用sr替换s中匹配的部分 |
| rx.FindString(s) | 返回匹配到的字符串 |
| rx.FindAllString(s, n) | 返回所有匹配到的字符串 []string类型 |

#### regexp包的用法示例

``` go
package main

import (
	"fmt"
	"regexp"
)

func main() {
	match, _ := regexp.MatchString("p[a-z]+ch", "peach")
	fmt.Println(match) // true

	r, _ := regexp.Compile("p[a-z]+ch")

	fmt.Println(r.MatchString("peach")) // true
	fmt.Println(r.FindString("peach punch")) // peach
	fmt.Println(r.FindStringIndex("peach punch")) // [0 5]
	fmt.Println(r.FindStringSubmatch("peach apple punch")) // [peach]
	fmt.Println(r.FindStringSubmatchIndex("peach apple punch")) // [0 5]
	fmt.Println(r.FindAllString("peach apple pinch", 1)) // [peach]
	fmt.Println(r.FindAllString("peach apple pinch", 2)) // [peach pinch]
}
```