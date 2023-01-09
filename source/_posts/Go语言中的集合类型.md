---
title: Go语言中的集合类型
date: 2022-12-27 14:26:17
tags:
  - go
  - regexp
categories:
  - 计算机基础
cover: /images/go.png
---
### 指针

- Go语言中只有值传递。
- 切片、映射、通道、函数等引用类型也是值传递，只不过它们的值是指针，所以对形参的改变也会反映到实参本身。
- 指针是指保存了另一个变量内存地址的变量。通过指针可以让参数传递的成本最低且内容可修改，而且可以让变量的生命周期独立于作用域。

### 数组和切片

- 使用如下语法创建数组。
``` Go
[length]Type
[N]Type{value1, value2, ..., valueN}
[...]Type{value1, value2, ..., valueN}
```
- 数组的长度是固定的，不可以修改。
- 数组的容量`cap()`和长度`len()`都等于数组的长度。
- 数组按值传递，及传递给函数的是数组的副本，而切片是引用类型，传递的是指针。

``` Go
package main

import "fmt"

func changeArray(a [3]int) {
	a[0] = 3
}

func main() {
	myarray := [3]int{1, 2, 3}
	changeArray(myarray)
	fmt.Println(myarray) // 因为myarray是数组，所以打印结果为 [1 2 3]
}
```
``` Go
package main

import "fmt"

func changeArray(a []int) {
	a[0] = 3
}

func main() {
	myarray := []int{1, 2, 3}
	changeArray(myarray)
	fmt.Println(myarray) // 因为myarray是切片，所以打印结果为 [3 2 3]
}
```
- 可以使用下面的方法创建切片。
``` Go
make([]Type, length, capacity)
make([]Type, length)
[]Type
[]Type(value1, value2, ..., valueN)
```
- 多个切片如果共享一个底层数组，那么对其中一个切片数值的改变，也会影响其他切片。
- 可以使用`for index, item := range s {}`来遍历切片s。
- 可以使用`s = append(s, "a", "b", "c")`或者`s = append(s, t...)`来将元素a, b, c和切片t追加到切片s中。
- 可以使用`nr = copy(s, t)`将t切片中的内容拷贝到s中。
- 可以使用标准库中的`sort`包来对排序和搜索切片。
``` Go
package main

import (
	"fmt"
	"sort"
)

func main() {
	myarray := []int{1, 3, 2}
	files := []string{"Test.conf", "utils.go", "Makefile"}
	target := "Makefile"
	fmt.Println(sort.IntsAreSorted(myarray))  // false
	sort.Ints(myarray)
	fmt.Println(myarray) //[1 2 3]

	sort.Strings(files)
	fmt.Printf("%q\n", files) // ["Makefile" "Test.conf" "utils.go"]
	i := sort.Search(len(files),
		func(i int) bool { return files[i] >= target })
	if i < len(files) && files[i] == target {
		fmt.Printf("found \"%s\" at files[%d]\n", target, i) // found "Makefile" at files[0]
	}
}
```

### 映射
- 创建映射的方式
	- `make(map[KeyType]ValueType, initialCapacity)`
	- `make(map[KeyType]ValueType`
	- `map[KeyType]ValueType{}`
	- `map[KeyType]ValueType{key1: value1, ..., keyN: valueN}`
- 映射的操作
	- `m[k] = v`: 赋值v给映射的键值k
	- `delete(m, k)`: 删除map中的k
	- `v := m[k]`: 将map中k对应的值赋值给v
	- `v, found := m[k]`: 如果k值不存在，将v赋值为0，found设为false
	- `len(m)`: 返回map中的键值对数目