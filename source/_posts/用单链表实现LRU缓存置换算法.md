---
title: '用单链表实现LRU缓存置换算法'
date: 2018-10-30 23:47:22
tags: [数据结构与算法]
published: true
hideInList: false
feature: 
isTop: false
categories: 计算机基础
---

#### 缓存置换算法所解决的问题

在存储系统的金字塔结构中，缓存的存取速度比内存快，然而成本比内存高，所以缓存的容量有限。缓存置换算法所要解决的问题便是在容量有限的缓存中，存放哪些数据可以提升缓存命中率。  

#### LRU缓存置换算法的核心思想

LRU算法认为最近访问过的数据被再次访问的可能性最大，所以缓存中存放的是最近使用过的数据。具体的做法是：

* 把缓存当做一个队列，队首的数据是最近被访问的数据，而队尾的数据则是即将被置换出缓存的数据。
* 每当有新数据被访问时，会在队列中查找该数据，如果存在，就被该数据挪到队首。
* 如果被访问的数据没有在队列（缓存）中，而且缓存未满，则该数据被放到队首。
* 如果被访问的数据没有在队列中，然而缓存已经满了，则把队尾的数据从队列中删除，再把新数据放置到队首。

#### 用C语言实现LRU缓存置换算法

``` c
#include <stdio.h>
#include <stdlib.h>

#define CACHE_SIZE 20

int nr_of_list = 0;

typedef struct CACHE_ITEM {
    int data;
    struct CACHE_ITEM *next;
} CACHE_ITEM;

CACHE_ITEM list_head;

int init_list_head(CACHE_ITEM *head) {
    if (!head) {
        printf("%s: parameter error - no list head\n", __func__);
        return -1;
    }
    head->data = 0;
    head->next = NULL;
    return 0;
}

int insert_node(CACHE_ITEM *head, CACHE_ITEM *node) {
    if (!head || !node) {
        printf("%s: parameter error - no list head or node\n", __func__);
        return -1;
    }
    node->next = head->next;
    head->next = node;
    nr_of_list += 1;
    return 0;
}

int remove_node(CACHE_ITEM *head, CACHE_ITEM *node) {
    if (!head || !node) {
        printf("%s: parameter error - no list head or node\n", __func__);
        return -1;
    }
    CACHE_ITEM *p = head;
    while(p->next) {
        if(p->next == node) {
            p->next = node->next;
            nr_of_list -= 1;
        } else {
            p = p->next;
        }
    }
    return 0;
}

int remove_tail_node(CACHE_ITEM *head) {
    if (!head) {
        printf("%s: parameter error - no list head\n", __func__);
        return -1;
    }
    CACHE_ITEM *p = head;
    while(p->next) {
        if(p->next && !p->next->next) {
            p->next = NULL;
            nr_of_list -= 1;
        } else {
            p = p->next;
        }
    }
    return 0;
}

CACHE_ITEM *search_list(CACHE_ITEM *head, int number) {
    if (!head) {
        printf("%s: parameter error - no list head\n", __func__);
        return NULL;
    }
    CACHE_ITEM *p = head->next;
    CACHE_ITEM *q = NULL;
    while(p) {
        if(p->data == number) {
            q = p;
            break;
        } else {
            p = p->next;
        }
    }
    return q;
}

void show_list(CACHE_ITEM *head) {
    CACHE_ITEM *p = head->next;
    while(p) {
        printf("%d ", p->data);
        p = p->next;
    }
    printf("\n");
    printf("List Length: %d\n", nr_of_list);
}

int lru(CACHE_ITEM *head, int number) {
    if (!head) {
        printf("%s: parameter error - no list head\n", __func__);
        return -1;
    }
    CACHE_ITEM *p = search_list(head, number);
    if (p) {
        remove_node(head, p);
        insert_node(head, p);
    } else if (nr_of_list < CACHE_SIZE) {
        CACHE_ITEM *new_node = (CACHE_ITEM *)malloc(sizeof(CACHE_ITEM));
        new_node->data = number;
        insert_node(head, new_node);
    } else {
        remove_tail_node(head);
        CACHE_ITEM *new_node = (CACHE_ITEM *)malloc(sizeof(CACHE_ITEM));
        new_node->data = number;
        insert_node(head, new_node);
    }
    return 0;
}

int main(int argc, char **argv) {
    CACHE_ITEM *head = &list_head;
    init_list_head(head);
    int num = 0;
    while(1) {
        printf("请输入数字：\n");
        scanf("%d", &num);
        lru(head, num);
        show_list(head);
    }
    return 0;
}
```

#### 运行结果
``` bash
请输入数字：
1
1
List Length: 1
请输入数字：
2
2 1
List Length: 2
请输入数字：
3
3 2 1
List Length: 3
请输入数字：
4
4 3 2 1
List Length: 4
请输入数字：
5
5 4 3 2 1
List Length: 5
请输入数字：
3
3 5 4 2 1
List Length: 5
请输入数字：
1
1 3 5 4 2
List Length: 5
请输入数字：
6
6 1 3 5 4 2
List Length: 6
请输入数字：
7
7 6 1 3 5 4 2
List Length: 7
请输入数字：
8
8 7 6 1 3 5 4 2
List Length: 8
请输入数字：
9
9 8 7 6 1 3 5 4 2
List Length: 9
请输入数字：
10
10 9 8 7 6 1 3 5 4 2
List Length: 10
请输入数字：
11
11 10 9 8 7 6 1 3 5 4 2
List Length: 11
请输入数字：
12
12 11 10 9 8 7 6 1 3 5 4 2
List Length: 12
请输入数字：
13
13 12 11 10 9 8 7 6 1 3 5 4 2
List Length: 13
请输入数字：
14
14 13 12 11 10 9 8 7 6 1 3 5 4 2
List Length: 14
请输入数字：
15
15 14 13 12 11 10 9 8 7 6 1 3 5 4 2
List Length: 15
请输入数字：
16
16 15 14 13 12 11 10 9 8 7 6 1 3 5 4 2
List Length: 16
请输入数字：
17
17 16 15 14 13 12 11 10 9 8 7 6 1 3 5 4 2
List Length: 17
请输入数字：
18
18 17 16 15 14 13 12 11 10 9 8 7 6 1 3 5 4 2
List Length: 18
请输入数字：
19
19 18 17 16 15 14 13 12 11 10 9 8 7 6 1 3 5 4 2
List Length: 19
请输入数字：
20
20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 1 3 5 4 2
List Length: 20
请输入数字：
21
21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 1 3 5 4
List Length: 20
请输入数字：
22
22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 1 3 5
List Length: 20
请输入数字：
23
23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 1 3
List Length: 20
请输入数字：
1
1 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6 3
List Length: 20
请输入数字：
4
4 1 23 22 21 20 19 18 17 16 15 14 13 12 11 10 9 8 7 6
List Length: 20
```