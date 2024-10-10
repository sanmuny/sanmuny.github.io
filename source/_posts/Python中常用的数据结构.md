---
title: Python中常用的数据结构
tags:
  - python
  - 数据结构
categories: 计算机基础
published: true
hideInList: false
isTop: false
date: 2024-10-05 22:00:41
feature:
---

### 用deque实现队列

主要特点和优势：

- 高效的两端插入和删除操作：与列表相比，在队列的两端进行添加或删除元素的操作效率更高。时间复杂度为O(1)，而在列表的开头进行插入或删除操作的时间复杂度通常为O(n)，其中是列表的长度。
- 支持线程安全的追加和弹出：在多线程环境中，可以使用 deque 的 append 和 pop 方法，并且它们是线程安全的（需要适当的同步机制确保整体操作的线程安全）。
- 可设置最大长度：可以创建一个具有固定最大长度的 deque，当添加新元素超过最大长度时，会自动从另一端删除旧元素。

常用方法：

- 初始化：
``` python
d = collections.deque()  # 创建一个空的双向队列。
d = collections.deque([1, 2, 3])  # 用初始元素创建双向队列。
```
- 添加元素：
``` python
d.append(item)  # 在队列右端添加一个元素。
d.appendleft(item)  # 在队列左端添加一个元素。
```
- 删除元素：
``` python
d.pop()  # 删除并返回队列右端的元素。
d.popleft()  # 删除并返回队列左端的元素。
```
- 扩展队列：
``` python
d.extend(iterable)  # 在队列右端扩展多个元素。
d.extendleft(iterable)  # 在队列左端逆序扩展多个元素。
```
- 旋转：
``` python
d.rotate(n)  # 将队列中的元素向右旋转 n 步（如果 n 是负数，则向左旋转）。
```

### 用deque实现栈

``` python
from collections import deque

stack = deque()

stack.append(1)  # 入栈
stack.append(2)
stack.append(3)

print(f"Stack: {stack}")

popped_item = stack.pop()  # 出栈
print(f"Popped item: {popped_item}")
print(f"Stack after pop: {stack}")

if stack:  # 获取栈顶元素
    top_item = stack[-1]
    print(f"Top item: {top_item}")
else:
    print("Stack is empty.")
```

### 有序字典(OrderedDict)

#### 特点

- 顺序保持：与普通字典不同，OrderedDict 会按照元素插入的顺序来存储和遍历键值对。这在需要保留元素顺序的场景中非常有用，比如在处理配置文件、实现有序的计数器等方面。
- 可迭代性：可以像普通字典一样进行迭代操作，但迭代顺序与插入顺序一致。


#### 常用方法

- 初始化：
``` python
from collections import  # OrderedDict 首先导入 OrderedDict。
od = OrderedDict()  # 创建一个空的 OrderedDict。
od = OrderedDict([('key1', 'value1'), ('key2', 'value2')])  # 用初始键值对创建 OrderedDict。
```
- 插入元素：
``` python
od['new_key'] = 'new_value' # 向 OrderedDict 中插入新的键值对。
```
- 删除元素：
``` python
del od['key']  # 删除指定键的键值对。
od.pop('key')  # 删除指定键的键值对并返回对应的值。
```
- 移动元素：
``` python
od.move_to_end('key', last=True)  # 将指定键的键值对移动到末尾，如果 last=False 则移动到开头。
```

### 示例：实现LRU缓存置换算法

``` python
import collections

class LRUCache(collections.OrderedDict):

    def __init__(self, capacity: int):
        super().__init__()
        self.capacity = capacity

    def get(self, key: int) -> int:
        if key in self:
            self.move_to_end(key)
            return self[key]
        return -1


    def put(self, key: int, value: int) -> None:
        if key in self:
            self.move_to_end(key)
        self[key] = value
        if len(self) > self.capacity:
            self.popitem(last=False)
```

### 用heapq实现最大(小)堆

在 Python 中，`heapq`模块提供了对堆数据结构的支持。堆是一种特殊的数据结构，通常是一个完全二叉树，满足特定的堆性质。

#### 主要特点和用途

- 高效的最小（或最大）值查找：堆可以快速地找到并删除最小（或最大）值，时间复杂度为`O(logn)`，其中n是堆中元素的数量。这使得它在实现优先队列等场景中非常有用。
- 动态调整：可以方便地向堆中插入新元素或删除现有元素，同时保持堆的性质。
- 空间效率：堆通常使用数组实现，具有较高的空间效率。


#### 常用函数

- `heapify`：将一个列表转换为堆
- `heappush`：向堆中插入一个元素
- `heappop`：弹出并返回堆中的最小元素
- `nlargest`和`nsmallest`：返回列表中最大或最小的n个元素
