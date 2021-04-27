---
title: 线性表 | 数据结构
date: 2021-04-25 22:00:00
tags:
  - 数据结构
  - C
categories: 基础
---

线性表的定义、基本操作

<!-- more -->

## 线性表的定义

线性表是具有**相同类型**的 n 个数据元素的**有限序列**，其中 n 为**表长**，当 n=0 时线性表是一个**空表**。

若用 L 命名线性表，则其一般表示为：L=(a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>i</sub>, a<sub>i+1</sub>, a<sub>n</sub>)

**位序**：表示第 i 个元素，从 1 开始，而数组下标是从 0 开始。

**表头元素**：第 1 个元素。

**表尾元素**：第 n 个元素，最后一个元素。

除了表头元素外，每个元素有且仅有一个直接前驱；除了表尾元素外，每个元素有且仅有一个**直接后继**。

## 线性表的基本操作

为什么要实现对数据结构的基本操作？

1. 团队合作编程，你定义的数据结构要让别人能够很方便的使用（封装）。
2. 将常用的操作/运算封装称函数，避免重复工作，降低出错风险。

### 抽象的操作

**初始化表**：`InitList(&L)` 构造一个空的线性表 L，分配内存空间。

**销毁操作**：`DestroyList(&L)` 销毁线性表，并释放线性表 L 所占用的内存空间。

**插入操作**： `ListInsert(&L, i, e)` 在表 L 中的第 i 个位置上插入制定元素 e。

**删除操作**：`ListDelete(&L, i, &e)` 删除表 L 中第 i 个位置的元素，并用 e 返回删除元素的值。

**按值查找操作**：`LocateElem(L, e)` 在表 L 中查找具有给定关键字值的元素。

**按位查找操作**： `GetElem(L, i)` 获取表 L 中第 i 个位置的元素的值。

**求表长**：`Length(L)` 返回线性表 L 的长度，即 L 中数据元素的个数。

**输出操作**：`PrintList(L)` 按前后顺序输出线性表 L 中所有元素值。

**判空操作**：`Empty(L)` 若 L 为空表，则返回 `true`，否则返回 `false`。

### 顺序表的实现

**顺序表**：用顺序存储的方式实现线性表顺序存储。把逻辑上相邻的元素存储在物理位置上也相邻的存储单元中，元素之间的关系由存储单元的邻近关系来体现。

#### 静态分配

C 语言的定义：

```c
#define MAX_SIZE 10 // 定义数组的最大长度

typedef struct {
    ElemType data[MAX_SIZE];  // 用数组存放数据元素，ElemType 为制定的数据类型
    int length;               // 顺序表的当前长度
} SeqList;                    // 顺序表的类型定义
```

例如：

```c
#define MAX_SIZE 10

typedef struct {
    int data[MAX_SIZE];
    int lenght;
} SeqList;

// InitList 初始化一个顺序表
void InitList(SqList *L) {
    // 只需要将顺序表初始长度修改为 0 即可
    // 不需要将所有数据元素设置默认值
    L->length = 0;
}
```

#### 动态分配

C 语言的定义：

```c
#define INIT_SIZE 10 // 顺序表的初始长度

typedef struct {
    ElemTYpe *data; // 指示动态分配数组的指针
    int maxSize;    // 顺序表的最大容量
    int length;     // 顺序表的当前长度
} SeqList;          // 顺序表的类型定义
```

例如：

```c
#include <stdlib.h>

#define INIT_MAX 10

typedef struct {
    int *data;
    int maxSize;
    int length;
} SeqList;

// InitList 初始化一个顺序表
void InitList(SeqList *L) {
    // 使用 malloc 函数申请一片连续的存储空间
    L->data = (int *) malloc(INIT_MAX * sizeof(int));
    L->length = 0;
    L->maxSize = INIT_MAX;
}

// IncreaseSize 增加动态数组的长度
void IncreaseSize(SeqList *L, int len) {
    int *p = L->data;
    L->maxSize += len; // 顺序表最大长度增加 len
    L->data = (int *) malloc(L->maxSize * sizeof(int));
    for (int i = 0; i < L->length; i++) {
        L->data[i] = p[i]; // 将数据赋值到新区域
    }
    free(p); // 释放原来的内存空间
}
```

#### 顺序表的特点

1. 随机访问。可以在 O(1) 时间内找到第 i 个元素。
2. 存储密度高。每个节点只存储数据元素。
3. 扩展容量不方便。即便采用动态分配的方式实现，拓展长度的时间复杂度也比较高。
4. 插入、删除操作不方便，需要移动大量元素。

未完待续 ...

[^1 ]: [数据结构-王道考研](https://www.bilibili.com/video/BV1b7411N798)