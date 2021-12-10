---
title: '单链表反转'
date: 2018-12-07 14:43:11
tags: [数据结构与算法]
published: true
hideInList: false
feature: 
isTop: false
---

#### 实现单链表反转的思路

实现单链表反转的难点在于，如果让当前节点的next指向前驱节点，那么链表就断了，所以解决的办法就是在进行反转操作之前用一个临时指针变量保存后继节点的地址。

#### 单链表反转的代码实现

    #include <stdio.h>
    #include <stdlib.h>

    typedef struct LIST_NODE {
        int data;
        struct LIST_NODE* next;
    } LIST_NODE;

    void show\_list(LIST\_NODE *head) {
        LIST_NODE *p = head->next;

        while(p != NULL) {
            printf("%d ", p->data);
            p = p->next;
        }
        printf("\\n");
    }

    void reverse\_list(LIST\_NODE *head) {
        LIST_NODE \*p, \*q, *tmp;
        p = head;
        q = p->next;

        while (q != NULL) {
            tmp = q->next;
            if (p == head) {
                q->next = NULL;
            } else {
                q->next = p;
            }
            p = q;
            q = tmp;
        }
        head->next = p;
    }

    int main(int argc, char **argv) {
        LIST_NODE head = {
            .data = 0,
            .next = NULL
        };
        int data;
        LIST_NODE *tail = &head;
        while (1) {
            printf("Please input a number: (0 to stop input)\\n");
            scanf("%d", &data);
            if (data == 0) {
                break;
            }
            LIST\_NODE *new\_node = malloc(sizeof(LIST_NODE));
            new_node->data = data;
            new_node->next = NULL;
            tail->next = new_node;
            tail = new_node;
        }
        show_list(&head);
        reverse_list(&head);
        show_list(&head);
        return 0;
    }