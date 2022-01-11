---
title: Go语言中的字符串
date: 2021-12-16 19:24:00
tags:
  - go
categories:
  - 计算机基础
cover: /images/go.png
---

### 字符串语法基础

* Go语言中的字符串采用UTF-8编码格式
* 字符串字面量使用双引号`"`以及反引号 ` 来创建，区别在于双引号解析转义字符而反引号不解析

### 字符串操作符

Go语言中的字符串支持如下操作：

| 语法 | 描述 |
| --- | --- |
| s + t | 将字符串s和字符串t连接起来 |
| s += t | s = s + t |
| s[n] | 字符串s中索引为n的字节 uint8类型 |
| s[n:m] | 从索引n到m-1取得的子字符串 (按字节) |
| s[n:] | 从索引n到字符串结尾取得的子字符串 (按字节) |
| s[:m] | 从字符串开头到索引位置m-1取得的子字符串 (按字节) |
| len(s) | 计算字符串的字节数 |
| []rune(s) | 将字符串转化为码点(int32)数组 |
| len([]rune(s)) | 计算字符串的码点数, 即字符数 |
| string(chars) | 将一个码点数组转化为字符串 |
| []byte(s) | 将字符串转化为字节(uint8)数组 |
| string(bytes) | 将字节数组转化为字符串 |
| string(i) | 将任意类型数字转化为字符串 |
| strconv.Itoa(i) | 将int类型的数值转化为字符串，返回(字符串，err) |
| fmt.Sprint(x) | 任意类型的字符串表示，如x = 65，则返回"65" |

Go语言中的字符串也可以使用比较操作符 `<, <=, ==, !=, >, >=`。具体比较方法是将字符串转化成的字节数组中的字节逐一比较。

### 字符串的索引与切片

Go语言中可以通过字符串索引来获取单个字符，通过切片来获取子字符串。例如：
``` Go
package main
import "fmt"
func main() {
  s := "hello world"
  us := "hello 世界"
  fmt.Println(s[0]) // 106
  fmt.Println(s[0 : len(s)-1]) // "hello worl"
  fmt.Println(us[0 : len(s)-1]) // "hello 世�"
  fmt.Println(us[0 : len(s)-2]) // "hello 世"
}
```

### 字符串相关的包

#### fmt

fmt包可以用来格式化字符串。

| 语法 | 描述 |
| --- | --- |
| err := fmt.Errorf(format, args...) | 返回一个包含格式化字符串的错误值 |
| nrBytes, err := fmt.Fprint(writter, args...) | 将args按照%v和空格分隔写入writter |
| nrBytes, err := fmt.Fprintf(writter, format, args...) | 将格式化字符串写入writter |
| nrBytes, err := fmt.Fprintln(writter, args...) | 将args按照%v和空格分隔换行结尾写入writter |
| nrBytes, err := fmt.Print(args...) | 将args按照%v和空格分隔写入os.Stdout |
| nrBytes, err := fmt.Printf(format, args...) | 将格式化字符串写入os.Stdout |
| nrBytes, err := fmt.Println(args...) | 将args按照%v和空格分隔换行结尾写入os.Stdout |
| str := fmt.Sprint(args...) | 返回一个args按照%v和空格分隔转换的字符串 |
| str := fmt.Sprintf(format, args...) | 返回一个由格式化字符串转换的字符串 |
| str := fmt.Sprintln(args...) | 返回一个args按照%v和空格分隔换行结尾转换的字符串 |

例如：
``` Go
package main

import (
  "fmt"
)

func main() {
  arr := [...]interface{}{
    1,
    "hello world",
    2}
  nr := 5
  err := fmt.Errorf("Error: component %v crashed.", "ui")
  fmt.Println(err) //Error: component ui crashed.
  fmt.Print(arr, nr, "\n") //[1 hello world 2] 5
  fmt.Printf("Array: %v, number: %v\n", arr, nr) //Array: [1 hello world 2], number: 5
  fmt.Println(arr, nr) //[1 hello world 2] 5
  str := fmt.Sprint(arr, nr)
  fmt.Println(str) //[1 hello world 2] 5
}
```

字符串格式化指令列表如下：

| 指令 | 描述 |
| --- | --- |
| %% | 输出% |
| %b | 二进制整数值，或者用科学计数法表示的指数为2的浮点数 |
| %c | Unicode字符的码点值 |
| %d | 十进制数值 |
| %e | 用科学计数法e表示的浮点数或者复数值 |
| %E | 用科学计数法E表示的浮点数或者复数值 |
| %f | 浮点数或者复数值 |
| %g | %e或者%f表示的浮点数或者复数值 |
| %G | %E或者%f表示的浮点数或者复数值 |
| %o | 八进制数值 |
| %p | 十六进制表示的指针值 |
| %q | 使用Go语法必要时进行转义 |
| %s | UTF8编码的字符串 |
| %t | 布尔值 |
| %T | Go数值类型 |
| %U | unicode表示法码点 |
| %v | 默认格式输出的内置或者自定义值，默认使用自定义类型的String()方法 |
| %x | 十六进制数值 a-f |
| %X | 十六进制数值 A-F |

修饰符列表如下：

| 修饰符 | 描述 |
| --- | --- |
| # | %#v 使用Go语法将值自身输出，%#x 输出以0x打头的十六进制数，%#X 输出以0X打头的十六进制数 |
| + | 数值带正负号，字符串输出ASCII字符，结构体输出其字段名 |
| - | 向左对其，默认右对齐 |
| 0 | 以数字0填充而非空格填充 |
| n.m | n个字符输出浮点数或者复数，小数点后输出m个数字, n和m都可以用*代替，也都可以被省略 |

例如：

``` Go
package main

import (
  "fmt"
)

func main() {
  b := 37
  o := 41
  x := 3931
  d := 569
  fmt.Printf("|%b|%9b|%-9b|%09b|\n", b, b, b, b) //|100101|   100101|100101   |000100101|
  fmt.Printf("|%o|%#o|%#8o|%#+8o|%+08o|\n", o, o, o, o, -o) //|51|051|     051|    +051|-0000051|
  fmt.Printf("|%x|%X|%8x|%08x|%#04X|0x%04X|\n", x, x, x, x, x, x) //|f5b|F5B|     f5b|00000f5b|0X0F5B|0x0F5B|
  fmt.Printf("|%d|%06d|%+06d|\n", d, d, d) //|569|000569|+00569|
  fmt.Printf("|%d|%#04x|%U|%c|\n", 0x3A6, 934, '\u03A6', '\U000003A6') //|934|0x03a6|U+03A6|Φ|
}

```


#### strings

1. 字符串分隔相关的函数

| 函数 | 描述 |
| --- | --- |
| arr := strings.Split(string, "*") | *为分隔符，返回一个字符串类型的切片 |
| arr := strings.SplitN(string, "*", n) | n为分隔后切片的长度，即分隔次数+1，从左到右 |
| arr := strings.SplitAfter(string, "*") | 同Split，但返回的切片中字符串包含分隔符 |
| arr := strings.SplitAfterN(string, "*", n) | 同SplitAfter，但只分隔n-1次，从左到右，n为分隔后切片的长度 |
| arr := strings.Fields(string) | 从字符串空白处切分字符串 |
| arr := strings.FieldsFunc(string, f) | 按照f返回为true的地方进行切分 |

例如：

``` Go
package main

import (
  "fmt"
  "strings"
)

func main() {
  str := "Honesty*and*diligence*should*be*your*eternal*mates."
  fmt.Println(strings.Split(str, "*")) //[Honesty and diligence should be your eternal mates.]
  fmt.Println(strings.SplitAfter(str, "*")) //[Honesty* and* diligence* should* be* your* eternal* mates.]
  fmt.Println(strings.SplitN(str, "*", 2)) //[Honesty and*diligence*should*be*your*eternal*mates.]
  fmt.Println(strings.SplitAfterN(str, "*", 2)) //[Honesty* and*diligence*should*be*your*eternal*mates.]
}
```

2. 字符串替换相关的函数

| 函数 | 描述 |
| --- | --- |
| str := strings.Replace(str, old, new, n) | n为替换的次数，-1表示不限制 |
| str := strings.Map(f, str) | 按照func f(rune) rune{}规则替换str中的字符 |

例如：

``` Go
package main

import (
  "fmt"
  "strings"
)

func main() {
  str := "Honesty*and*diligence*should*be*your*eternal*mates."
  fmt.Println(strings.Replace(str, "*", " ", -1)) //Honesty and diligence should be your eternal mates.
  fmt.Println(strings.Replace(str, "*", " ", 1)) //Honesty and*diligence*should*be*your*eternal*mates.
  fmt.Println(strings.Map(func(c rune) rune {
    if c == '*' {
      return ' '
    }
    return c
  }, str)) //Honesty and diligence should be your eternal mates.
}
```

3. 字符串过滤相关的函数

| 函数 | 描述 |
| --- | --- |
| str := strings.Trim(string, t) | 从string两端去掉t，返回新的字符串 |
| str := strings.TrimSpace(string) | 从string两端去掉空格 |
| str := strings.TrimFunc(string, f) | 从string两端过滤掉f返回为true的字符 |
| str := strings.TrimLeft(string, t) | 从string左边过滤掉t |
| str := strings.TrimLeftFunc(string, f) | 从string左边过滤掉f返回为true的字符 |
| str := strings.TrimRight(string, t) | 从string右边过滤掉t |
| str := strings.TrimRightFunc(string, f) | 从string右边过滤掉f返回为true的字符 |


``` Go
package main

import (
  "fmt"
  "strings"
)

func main() {
  str := "  Honesty and diligence should be your eternal mates.  "
  str1 := "**Honesty and diligence should*be your eternal mates.**"
  fmt.Println(strings.TrimSpace(str)) //Honesty and diligence should be your eternal mates.
  fmt.Println(strings.TrimLeft(str1, "*")) //Honesty and diligence should*be your eternal mates.**
  fmt.Println(strings.TrimRight(str1, "*")) //**Honesty and diligence should*be your eternal mates.
  fmt.Println(strings.Trim(str1, "*")) //Honesty and diligence should*be your eternal mates.
  fmt.Println(strings.TrimFunc(str1, func(c rune) bool {
    switch c {
      case '*':
      return true
    }
    return false
  })) //Honesty and diligence should*be your eternal mates.
}
```

4. 字符串判断相关的函数

| 函数 | 描述 |
| --- | --- |
| b := strings.Contains(s, t) | 如果s中包含t，则返回true |
| nr := strings.Count(s, t) | t在s中出现的次数 |
| b := strings.EqualFold(s, t) | 如果字符串s与t相等的话返回true，区分大小写 |
| b := strings.HasPrefix(s, t) | 如果s以t开头，则返回true |
| b := strings.HasSuffix(s, t) | 如果s以t结尾，则返回true |
| idx := strings.Index(s, t) | t在s中第一次出现的索引位置 |
| idx := strings.IndexAny(s, t) | t中的任意字符在s中第一次出现的索引位置 |
| idx := strings.IndexFunc(s, f) | s中第一次令f返回true的索引位置 |
| idx := strings.IndexRune(s, char) | 字符char在s中第一次出现的索引位置 |
| idx := strings.LastIndex(s, t) | t在s中最后一次出现的索引位置 |
| idx := strings.LastIndexAny(s, t) | t中的任意字符在s中最后一次出现的索引位置 |
| idx := strings.LastIndexFunc(s, f) | s中最后一次令f返回true的索引位置 |
| idx := strings.LastIndexRune(s, char) | 字符char在s中最后一次出现的索引位置 |


例如：

``` Go
package main

import (
  "fmt"
  "strings"
)

func main() {
  s := "hello world"
  t := "world"
  fmt.Println(strings.Index(s, t)) //6
  fmt.Println(strings.IndexAny(s, t)) //2
  fmt.Println(strings.IndexRune(s, 'e')) //1
}
```

5. 字符串修改相关的函数

| 函数 | 描述 |
| --- | --- |
| str := strings.Join(arr, t) | 用t作为分隔符将arr中的字符串连接起来 |
| str := strings.Repeat(s, nr) | 将s重复nr次返回新的字符串 |
| str := strings.Title(s) | 将s中每个单词的首字母大写 |
| str := strings.ToTitle(s) | 将s中的所有字母大写 |
| str := strings.ToLower(s) | 将s中所有的字母转换为小写字母 |
| str := strings.ToLowerSpecial(r unicode.SpecialCase, s) | 按照r将s中的unicode字符转换为小写 |
| str := strings.ToUpper(s) | 将s中所有的字母转换为大写字母 |
| str := strings.ToUpperSpecial(r unicode.SpecialCase, s) | 按照r将s中的unicode字符转换为大写 |

例如：

``` Go
package main

import (
  "fmt"
  "strings"
  "unicode"
)

func main() {
  str := "Honesty and diligence should be your eternal mates."
  str1 := "wow"
  str2 := "Önnek İş"
  arr := []string{"Honesty", "and", "diligence", "should", "be", "your", "eternal", "mates."}
  fmt.Println(strings.Join(arr, " ")) //Honesty and diligence should be your eternal mates.
  fmt.Println(strings.Repeat(str1, 3)) //wowwowwow
  fmt.Println(strings.Title(str)) //Honesty And Diligence Should Be Your Eternal Mates.
  fmt.Println(strings.ToTitle(str)) //HONESTY AND DILIGENCE SHOULD BE YOUR ETERNAL MATES.
  fmt.Println(strings.ToLower(str)) //honesty and diligence should be your eternal mates.
  fmt.Println(strings.ToUpper(str)) //HONESTY AND DILIGENCE SHOULD BE YOUR ETERNAL MATES.
  fmt.Println(strings.ToLowerSpecial(unicode.TurkishCase, str2)) //önnek iş
}
```

#### strconv

strconv包提供了许多可以在字符串和其他类型之间转换的函数。

|函数|描述|
|---|---|
|strconv.AppendBool(bs, b)|bs是byte类型的切片，返回的切片会将布尔值b的字符串表示追加到bs|
|strconv.AppendFloat(bs, f, fmt, prec, bits)|返回的切片会将f的字符串表示追加到bs|
|strconv.AppendInt(bs, i, base)|返回的切片会将整数i的字符串表示追加到bs|
|strconv.AppendQuote(bs, str)|返回的切片会将str的字符串表示（带双引号）追加到bs|
|strconv.AppendQuoteRune(bs, char)|返回的切片会将char的字符串表示（带单引号）追加到bs|
|strconv.AppendQuoteRuneToASCII(bs, char)|返回的切片会将char的字符串表示（非ascii字符用unicode十六进制表示）追加到bs|
|strconv.AppendQuoteToASCII(bs, str)|返回的切片会将str的字符串表示（非ascii字符用unicode十六进制表示）追加到bs|
|strconv.AppendUInt(bs, u, base)|返回的切片会将无符号数整型的字符串表示追加到bs|
|strconv.Atoi(s)|返回s转换的整型|
|strconv.CanBackquote(s)|检查s是否是一个合法的字符串常量（不包含反引号）|
|strconv.FormatBool(tf)|将布尔值转换为字符串"true"或者"false"|
|strconv.FormatFloat(f, fmt, prec, bits)|将浮点数f转换成字符串，fmt为转换格式，prec表示小数点后的位数，bits通常是64|
|strconv.FormatInt(i, base)|将整型数转换为字符串|
|strconv.FormatUint(u, base)|将无符号数转换为字符串|
|strconv.Itoa(i)|将十进制数i转换为字符串|
|strconv.IsPrint(c)|判断字符c是否可打印字符|
|strconv.ParseBool(s)|将字符串转换为布尔值，true: "1","t","T","true","True","TRUE". false: "0","f", "F","false","FALSE","False"|
|strconv.ParseFloat(s, bits)|将字符串转换为浮点类型的数值|
|strconv.ParseInt(s, base, bits)|base为0，自动判断进制，bits为0，则转换成int类型|
|strconv.ParseUint(s, base, bits)|同上，但返回无符号整型|
|strconv.Quote(s)|给字符串加双引号|
|strconv.QuoteRune(char)|给字符加上单引号|
|strconv.QuoteRuneToASCII(char)|同上，对非ASCII字符进行转义|
|strconv.QuoteToASCII(s)|同Quote，但对非ASCII字符进行转义|
|strconv.Unquote(s)|将s中的表示字符串、字符变量的单引号、反引号、双引号去掉|
|strconv.UnquoteChar(s, c)|s是目标字符串，c为0则不对单引号双引号进行转义，c为单引号则表示对单引号进行转义，为双引号则表示对双引号进行转义. 有4个返回值，第一个是s中第一个字符的rune值，第二个返回值表示s中第一个字符是否多字节unicode字符，第三个返回值表示剩下的字符串，第四个为error|

例子：
``` Go
package main

import (
  "fmt"
  "strconv"
)

func main() {
  bs := make([]byte, 10)
  fmt.Println(strconv.AppendBool(bs, false)) //[0 0 0 0 0 0 0 0 0 0 102 97 108 115 101]
  fmt.Println(strconv.AppendInt(bs, 22, 10)) //[0 0 0 0 0 0 0 0 0 0 50 50]
  fmt.Println(string(strconv.AppendFloat(bs, 12.65, 'e', 5, 64))) //1.26500e+01
  fmt.Println(string(strconv.AppendQuote(bs, "hello world"))) //"hello world"
  fmt.Println(string(strconv.AppendQuoteRune(bs, '我'))) //'我'
  fmt.Println(string(strconv.AppendQuoteRune(bs, 'd'))) //'d'
  fmt.Println(string(strconv.AppendQuoteRuneToASCII(bs, '我'))) //'\u6211'
  fmt.Println(strconv.Atoi("123")) //123 <nil>
  fmt.Println(strconv.Itoa(123)) //123
  fmt.Println(strconv.CanBackquote("hello world`")) //false
  fmt.Println(strconv.CanBackquote("hello world")) //true
  fmt.Println(strconv.FormatBool(true)) //true
  fmt.Println(strconv.FormatBool(false)) //false
  fmt.Println(strconv.FormatFloat(12.625, 'f', 2, 64)) //12.62
  fmt.Println(strconv.FormatInt(123, 10)) //123
  fmt.Println(strconv.IsPrint(50)) //true
  fmt.Println(string(strconv.AppendQuoteRune(bs, 50))) //'2'
  fmt.Println(strconv.ParseBool("True")) //true <nil>
  fmt.Println(strconv.ParseFloat("1.32", 64)) //1.32 <nil>
  fmt.Println(strconv.ParseInt("-0x25", 0, 0)) //-37 <nil>
  fmt.Println(strconv.ParseUint("-0x25", 0, 0)) //0 strconv.ParseUint: parsing "-0x25": invalid syntax
  fmt.Println(strconv.Quote("hello world")) //"hello world"
  fmt.Println(strconv.QuoteRune('我')) //'我'
  fmt.Println(strconv.QuoteRuneToASCII('我')) //'\u6211'
  fmt.Println(strconv.QuoteToASCII("我am")) //"\u6211am"
  fmt.Println(strconv.Unquote("\"我是谁\"")) //我是谁 <nil>
  fmt.Println(strconv.UnquoteChar(`"Fran & Freddie's Diner"`, 0)) //34 false Fran & Freddie's Diner" <nil>
}
```

#### utf8

utf8包中的函数主要用来查询和操作UTF-8编码的字符串或者字节切片。

b: []byte
s: string
c: rune

|函数|描述|
|---|---|
|utf8.DecodeRune(b)|返回b中的第一个rune及占用的字节数|
|utf8.DecodeRuneInString(s)|返回字符串中第一个rune及其占用的字节数|
|utf8.DecodeLastRune(b)|返回b中最后一个rune及其占用的字节数|
|utf8.DecodeLastRuneInString(s)|返回字符串s中最后一个rune及其占用的字节数|
|utf8.FullRune(b)|如果b的第一个rune是完整的utf8编码的话返回true|
|utf8.FullRuneInString(s)|同上，但输入是字符串|
|utf8.RuneCount(b)|b中rune的个数|
|utf8.RuneCountInString(s)|s中rune的个数|
|utf8.RuneLen(c)|对c进行编码需要的字节数|
|utf8.RuneStart(x byte)|x是否可以作为一个rune的第一个字节|
|utf8.Valid(b)|b中的字节能否正确表示一个utf8编码的字符串|
|utf8.ValidString(s)|s中的字节能否正确表示一个utf8编码的字符串|

例如：
``` Go
package main

import (
  "fmt"
  "unicode/utf8"
)

func main() {
  s := "hello 世界"
  b := []byte(s)
  c := '世'
  buf := []byte{228, 184, 150} // 世
  str := "世"

  fmt.Println(utf8.DecodeRune(b))  //104 1
  fmt.Println(utf8.DecodeLastRune(b)) //30028 3
  fmt.Println(utf8.DecodeRuneInString(s)) //104 1
  fmt.Println(utf8.DecodeLastRuneInString(s)) //30028 3
  fmt.Println(utf8.EncodeRune(b, c)) //3
  fmt.Println(string(b)) //世lo 世界
  b = []byte(s)

  fmt.Println(utf8.FullRune(buf)) //true
  fmt.Println(utf8.FullRune(buf[:2])) //false
  fmt.Println(utf8.FullRuneInString(str)) //true
  fmt.Println(utf8.FullRuneInString(str[:2])) //false

  fmt.Println(utf8.RuneCount(b)) //8
  fmt.Println(len(b)) //12
  fmt.Println(utf8.RuneCountInString(s)) //8
  fmt.Println(len(s)) //12
  fmt.Println(utf8.RuneLen(c)) //3

  fmt.Println(utf8.RuneStart(b[0])) //true
  fmt.Println(utf8.RuneStart(b[6])) //true
  fmt.Println(utf8.RuneStart(b[7])) //false
  fmt.Println(utf8.Valid(b)) //true
  fmt.Println(utf8.ValidString(s)) //true
}

```

#### unicode

unicode包主要提供了一个判断Unicode码点的函数。

c: rune

|函数|描述|
|---|---|
|unicode.IsControl(c)|是否控制字符|
|unicode.IsDigit(c)|是否十进制数字|
|unicode.IsGraphic(c)|是否图形字符|
|unicode.IsLetter(c)|是否字母|
|unicode.IsLower(c)|是否小写字母|
|unicode.IsMark(c)|是否标记|
|unicode.IsPrint(c)|是否可打印|
|unicode.IsPunct(c)|是否标点符号|
|unicode.IsSpace(c)|是否空格|
|unicode.IsSymbol(c)|是否符号|
|unicode.IsTitle(c)|是否标题大写字符|
|unicode.IsUpper(c)|是否大写字母|
|unicode.ToLower(c)|转换为小写字母|
|unicode.ToUpper(c)|转换为大写字母|
|unicode.ToTitle(c)|转换为标题字母|


``` Go
package main

import (
	"fmt"
	"unicode"
)

func main() {
	// constant with mixed type runes
	const mixed = "\b5Ὂg̀9! ℃ᾭG十"
	for _, c := range mixed {
		fmt.Printf("For %q:\n", c)
		if unicode.IsControl(c) {
			fmt.Println("\tis control rune")
		}
		if unicode.IsDigit(c) {
			fmt.Println("\tis digit rune")
		}
		if unicode.IsGraphic(c) {
			fmt.Println("\tis graphic rune")
		}
		if unicode.IsLetter(c) {
			fmt.Println("\tis letter rune")
		}
		if unicode.IsLower(c) {
			fmt.Println("\tis lower case rune")
		}
		if unicode.IsMark(c) {
			fmt.Println("\tis mark rune")
		}
		if unicode.IsNumber(c) {
			fmt.Println("\tis number rune")
		}
		if unicode.IsPrint(c) {
			fmt.Println("\tis printable rune")
		}
		if !unicode.IsPrint(c) {
			fmt.Println("\tis not printable rune")
		}
		if unicode.IsPunct(c) {
			fmt.Println("\tis punct rune")
		}
		if unicode.IsSpace(c) {
			fmt.Println("\tis space rune")
		}
		if unicode.IsSymbol(c) {
			fmt.Println("\tis symbol rune")
		}
		if unicode.IsTitle(c) {
			fmt.Println("\tis title case rune")
		}
		if unicode.IsUpper(c) {
			fmt.Println("\tis upper case rune")
		}
	}
}
```

输出：
```
For '\b':
	is control rune
	is not printable rune
For '5':
	is digit rune
	is graphic rune
	is number rune
	is printable rune
For 'Ὂ':
	is graphic rune
	is letter rune
	is printable rune
	is upper case rune
For 'g':
	is graphic rune
	is letter rune
	is lower case rune
	is printable rune
For '̀':
	is graphic rune
	is mark rune
	is printable rune
For '9':
	is digit rune
	is graphic rune
	is number rune
	is printable rune
For '!':
	is graphic rune
	is printable rune
	is punct rune
For ' ':
	is graphic rune
	is printable rune
	is space rune
For '℃':
	is graphic rune
	is printable rune
	is symbol rune
For 'ᾭ':
	is graphic rune
	is letter rune
	is printable rune
	is title case rune
For 'G':
	is graphic rune
	is letter rune
	is printable rune
	is upper case rune
For '十':
	is graphic rune
	is letter rune
	is printable rune
```