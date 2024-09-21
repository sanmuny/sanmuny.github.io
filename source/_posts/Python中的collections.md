---
title: Python中的collections
tags:
  - python
categories: 计算机基础
published: true
hideInList: false
isTop: false
date: 2024-09-21 17:16:47
feature:
---

在 Python 中，collections是一个非常有用的内置模块，它提供了一些高性能的数据类型，可以帮助你更高效地处理数据。

### namedtuple

命名元组是一种可以为元组中的每个元素指定名称的数据类型。这使得代码更加可读，并且可以像访问对象属性一样访问元组的元素。

``` python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
p = Point(1, 2)
print(p.x, p.y)
```

### deque

双端队列是一种可以在两端进行高效插入和删除操作的数据结构。它比列表更适合用于实现队列和栈。

``` python
from collections import deque

dq = deque([1, 2, 3])
dq.append(4)
dq.appendleft(0)
print(dq)
```

### Counter

计数器是一种用于统计可哈希对象出现次数的数据类型。它可以方便地统计列表、字符串等数据中的元素出现次数。

``` python
from collections import Counter

c = Counter('abracadabra')
print(c)
```

### OrderedDict

有序字典是一种可以记住插入顺序的字典类型。在 Python 3.7 及以后的版本中，普通字典也已经可以记住插入顺序，但在之前的版本中，OrderedDict非常有用。

``` python
from collections import OrderedDict

od = OrderedDict()
od['a'] = 1
od['b'] = 2
od['c'] = 3
print(od)
```

### 用OrderedDict实现LRU缓存算法

#### 描述

请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：

- LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
- int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
- void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
- 函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

示例：

输入
```
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
```
输出
```
[null, null, null, 1, null, -1, null, -1, 3, 4]
```

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4

#### 不用OrderedDict实现

``` python
class ListNode:
    def __init__(self):
        self.value = None
        self.key = None
        self.next = None
        self.prev = None

class LRUCache:

    def __init__(self, capacity: int):
        self.capacity = capacity
        self.head = ListNode()
        self.tail = ListNode()
        self.head.next = self.tail
        self.tail.prev = self.head
        self.nodeMap = {}

    def _putToHead(self, node):
        if node.prev is self.head:
            return
        node.next.prev = node.prev
        node.prev.next = node.next
        self.head.next.prev = node
        node.next = self.head.next
        node.prev = self.head
        self.head.next = node

    def get(self, key: int) -> int:
        if key in self.nodeMap:
            n = self.nodeMap[key]
            self._putToHead(n)
            return n.value
        return -1


    def put(self, key: int, value: int) -> None:
        if key in self.nodeMap:
            n = self.nodeMap[key]
            n.value = value
            self._putToHead(n)
            return
        n = ListNode()
        n.value = value
        n.key = key
        self.head.next.prev = n
        n.next = self.head.next
        n.prev = self.head
        self.head.next = n
        self.nodeMap[key] = n
        if len(self.nodeMap.keys()) > self.capacity:
            tailNode = self.tail.prev
            self.tail.prev = tailNode.prev
            tailNode.prev.next = tailNode.next
            if tailNode.key in self.nodeMap:
                del self.nodeMap[tailNode.key]
```

#### 使用OrderedDict实现

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
