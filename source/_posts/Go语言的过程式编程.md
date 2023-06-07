---
title: Go语言的过程式编程
date: 2023-03-08 18:35:45
tags:
  - go
  - regexp
categories:
  - 计算机基础
cover: /images/go.png
---

### Go语言中常用的内置函数

| 函数 | 用法 |
| --- | --- |
| append(s, ...) | 将s后面的所有参数追加到切片s中，如果容量不够，则新建一个切片 |
| cap(x) | 返回切片或者通道的容量，数组的长度 |
| len(x) | 返回切片、数组或者通道的长度 |
| close(ch) | 关闭通道，即不可以再往通道中写入值，但还可以读取 |
| complex(r, i) | 生成一个复数 |
| copy(dist, src) | 将切片src中的项复制到切片dist中，或者讲字符串src复制到[]byte类型的切片dist中 |
| delete(m, k) | 从映射m中删除键值为k的项 |
| real(cx) | 返回复数cx的实部 |
| imag(cx) | 返回复数cx的虚部 |
| make(T, l, c) | 创建一个切片、通道或者映射 |
| new(T) | 一个指向类型为T的值的指针 |
| panic(x) | 抛出一个运行时异常，其值为x |
| recover() | 捕获一个运行时异常 |

### Go语言赋值

1. Go语言中的自增自减运算符都是后置的，且没有返回值。
2. 可以使用`=`来给变量赋值，如果前面没有加`var`，那么变量必须是已经存在的。
3. 可以使用逗号同时给多个变量赋值。`a, b, c = 2, 3, 4`。
4. 可以使用`_`来忽略赋值，它与任意类型兼容。
5. 可以使用`:=`来同时声明和赋值一个变量。
6. 当使用逗号和`:=`来给多个变量赋值时，要求其中至少有一个变量是新建的。
7. 如果函数声明了返回值变量的名字，那么它在刚开始的时候会被初始化为其类型的零值。
8. 函数在返回的时候如果没有明确的指定变量名，那么它将返回函数声明中的变量。

### 类型

- Go语言使用`result := Type(expr)`来进行类型转换。

``` Go
package main
import "fmt"
func main() {
	runeSlice := []rune{'a', 'b', 'c'}
	str := string(runeSlice)
	fmt.Println(runeSlice)  // [97 98 99]
	fmt.Println(str) // abc
}
```

- `interface{}`类型用于表示空接口，它可以表示任意类型的值。
- `expr.(Type)`用于判断expr是不是类型Type。安全类型的成功判断返回expr的值和true，安全类型的失败判断返回该类型的零值和false。非安全类型的成功判断返回expr的值，失败触发panic。

``` Go
package main

import "fmt"

func main() {
	var i interface{} = 99
	var s interface{} = []string{"left", "right"}
	j := i.(int)
	fmt.Printf("%T -> %d\n", i, j)  // int -> 99
	fmt.Printf("%T -> %q\n", s, s)  // []string -> ["left" "right"]
}
```

### 分支

- `if`语句可以有可选的声明语句，在该语句中定义的变量其作用域限制在if语句之类。

``` Go
if optionalStatement; booleanExpression {
	block
} else if optionalStatement1; booleanExpression1 {
	block1
} else {
	block2
}
```

- `switch`语句默认不会向下贯穿，需要使用`fallthrough`语句。

``` Go
switch optionalStatement; optionalExpression {
case expressionList1: block1
...
default: blockN
}
```

例如:

``` Go
switch Suffix(file) {
case ".gz":
  return GzipFileList(file)
case ".tar", ".tar.gz", ".tgz":
  return TarFileList(file)
case ".zip":
  return ZipFileList(file)
}
```

- `switch`语句也可以用作类型开关，例如：

``` Go
switch x.(type) {
case bool: block0
case int, int8, int16: block1
default: blockN
}
```

### for循环语句

- 无限循环

``` Go
for { block }
```

- 普通循环

``` Go
for optionalPrestatement; booleanExpress; optionalPoststatement { block }
```

- 遍历字符串

``` Go
for idx, char := range aString { block }
for idx := range aString { block }
```

- 遍历数组或切片

``` Go
for idx, item := range anArrayOrSlice { block }
for idx := range anArrayOrSlice { block }
```

- 遍历映射

``` Go
for key, value := range aMap { block }
for key := range aMap { block }
```

- 遍历通道

``` Go
for item := range aChannel { block }
```

### select语句

select语句用来从多个通道中接收或发送数据。其语法如下：

``` Go
select {
case sendOrReceive1: block1
...
case sendOrReceive2: block2
default: blockD
}
```
如果没有default语句，那么select语句是阻塞的，直到其中的一个channel可以执行。如果有default语句，那么select语句是非阻塞的，如果没有channel满足条件则会执行默认语句。

### defer语句

defer语句的执行时机是：
- 所在函数返回之前，返回值计算完成之后执行。
- 多个defer语句以LIFO(Last In First Out)的方式执行。

``` Go
package main

import "fmt"

func main() {
  fmt.Println(add1(1)) // 10
}

func add1(a int) (result int) {
	defer func () { result = 10 }()
  defer func () { result = 20 }()
	result = a + 1
	return result
}
```

### panic和recover函数

不同于错误，异常是指不可能发生的情况发生了，通常是程序本身的问题，我们希望尽早发现问题所以通过调用panic函数抛出异常。panic被调用后，调用函数会中止执行，然后所有延迟执行的语句会执行，最后返回到上一层调用函数重复这样的过程直到main函数终止程序。
recover函数可以捕获异常并终止panic函数的冒泡。

例如：

``` Go
func ConvertInt64ToInt(x int64) int {
	if math.MinInt32 <= x && x <= math.MaxInt32 {
		return int(x)
	}
	panic(fmt.Sprintf("%d is out of the int32 range", x))
}

func IntFromInt64(x int64) (i int, err error) {
	defer func(){
		if e:= recover(); e != nil {
			err = fmt.Errorf("%v", e)
		}
	}()
	i = ConvertInt64ToInt(x)
	return i, nil
}
```