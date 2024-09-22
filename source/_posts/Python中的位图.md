---
title: Python中的位图
tags:
  - python
  - 位图
categories: 计算机基础
published: true
hideInList: false
isTop: false
date: 2024-09-22 12:48:33
feature:
---

在 Python 中，位图（Bitmap）是一种用于表示二进制数据的数据结构。它可以高效地存储和操作大量的布尔值（True/False）。

### 位图的基本概念

位图通常由一个字节数组或位序列组成，其中每个位表示一个特定的状态或属性。例如，可以使用位图来表示一组整数是否存在于某个集合中，或者表示某个图形中的像素是否被选中。

### Python 中实现位图的方法

1. 使用内置的bytearray类型

bytearray是一个可变的字节序列，可以用来存储位图数据。每个字节可以表示 8 个位，通过位操作可以设置、清除和检查特定的位。
例如：

``` python
bitmap = bytearray(10)  # 创建一个可以存储 80 个位的位图
# 设置第 5 个位为 1
bitmap[5 // 8] |= 1 << (5 % 8)
# 检查第 5 个位是否为 1
is_set = (bitmap[5 // 8] & (1 << (5 % 8)))!= 0
```

2. 使用第三方库
bitarray库提供了一个更方便的位序列数据结构，可以高效地进行位操作。
安装：pip install bitarray
例如：
``` python
from bitarray import bitarray

bitmap = bitarray(100)  # 创建一个长度为 100 的位序列
bitmap[5] = True  # 设置第 5 个位为 1
is_set = bitmap[5]  # 检查第 5 个位是否为 1
```

### 位图的应用场景

- 集合操作: 可以用位图来表示集合，进行快速的集合交集、并集和差集等操作。例如，判断两个整数集合是否有交集，可以将两个集合分别表示为位图，然后对两个位图进行按位与操作，如果结果不为 0，则表示有交集。
- 内存高效的数据存储: 当需要存储大量的布尔值时，位图可以比使用列表或字典等数据结构更节省内存。
- 图形处理: 在图像处理中，位图可以用来表示像素的颜色或透明度等属性。
- 数据库索引: 可以使用位图索引来加速数据库查询，特别是对于包含大量布尔属性的数据集。

### 举例：打印列表中的重复数字

https://leetcode.cn/problems/find-all-duplicates-in-an-array/description/

给你一个长度为 n 的整数数组 nums ，其中 nums 的所有整数都在范围 [1, n] 内，且每个整数出现 一次 或 两次 。请你找出所有出现 两次 的整数，并以数组形式返回。你必须设计并实现一个时间复杂度为 O(n) 且仅使用常量额外空间（不包括存储输出所需的空间）的算法解决此问题。

示例 1：

输入：nums = [4,3,2,7,8,2,3,1]
输出：[2,3]

示例 2：

输入：nums = [1,1,2]
输出：[1]

示例 3：

输入：nums = [1]
输出：[]

提示：

n == nums.length
1 <= n <= 105
1 <= nums[i] <= n
nums 中的每个元素出现 一次 或 两次

``` python
def setBit(bitMap, num):
    idx = num // 8
    offset = num % 8
    if idx >= len(bitMap):
        return False
    bitMap[idx] |= 1 << offset

def isSet(bitMap, num):
    idx = num // 8
    offset = num % 8
    if idx >= len(bitMap):
        return False
    return (bitMap[idx] & (1 << offset)) != 0

class Solution:
    def findDuplicates(self, nums: List[int]) -> List[int]:
        lenNums = len(nums)
        result = []
        bitMap = bytearray(lenNums // 8 + 1)
        for num in nums:
            if isSet(bitMap, num):
                result.append(num)
            else:
                setBit(bitMap, num)
        return result
```