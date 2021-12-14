---
title: 'Go基本数据类型及常变量声明'
date: 2021-10-20 17:59:00
tags: [go]
published: false
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---
Go支持如下内置基本类型：

* 一种内置布尔类型：bool
* 11种内置整数类型：int8、uint8、int16、uint16、int32、uint32、int64、uint64、int、uint和uintptr
* 两种内置浮点数类型：float32和float64
* 两种内置复数类型：complex64和complex128
* 一种内置字符串类型：string

Go中有两种内置类型别名（type alias）：

* byte是uint8的内置别名。 我们可以将byte和uint8看作是同一个类型。
* rune是int32的内置别名。 我们可以将rune和int32看作是同一个类型。

 uintptr、int以及uint类型的值的尺寸依赖于具体编译器实现。 通常地，在64位的架构上，int和uint类型的值是64位的；在32位的架构上，它们是32位的。 编译器必须保证uintptr类型的值的尺寸能够存下任意一个内存地址。

一个complex64复数值的实部和虚部都是float32类型的值。 一个complex128复数值的实部和虚部都是float64类型的值。 

```
// 一些类型定义声明
type status bool     // status和bool是两个不同的类型
type MyString string // MyString和string是两个不同的类型
type Id uint64       // Id和uint64是两个不同的类型
type real float32    // real和float32是两个不同的类型

// 一些类型别名声明
type boolean = bool // boolean和bool表示同一个类型
type Text = string  // Text和string表示同一个类型
type U8 = uint8     // U8、uint8和 byte表示同一个类型
type char = rune    // char、rune和int32表示同一个类型
```

### 零值
每种类型都有一个零值。一个类型的零值可以看作是此类型的默认值。

* 一个布尔类型的零值表示真假中的假。
* 数值类型的零值都是零（但是不同类型的零在内存中占用的空间可能不同）。
* 一个字符串类型的零值是一个空字符串。

