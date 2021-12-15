---
title: 'ES6: Set 与 Map'
date: 2018-10-15 16:35:11
tags: [es6,javascript]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

Set

*   let my_set = new Set();
*   let my_set = new Set(\[1, 1, 2, 2\]);
*   my_set.add(5);
*   my_set.delete(5);
*   my_set.has(5);
*   my_set.forEach(function(value){});
*   let my_set = WeakSet() // 只允许对象作为set的元素，便于垃圾回收

Map

*   let my_map = new Map();
*   let map = new Map(\[\["name", "Nicholas"\], \["age", 25\]\]);
*   my_map.set(key, value);
*   my_map.get(key)
*   my_map.has(key)
*   my_map.delete(key);
*   my_map.clear();
*   my_map.size;
*   my_map.forEach(function(value, key, ownerMap) { console.log(key + " " + value); console.log(ownerMap === map); });
*   let map = new WeakMap();