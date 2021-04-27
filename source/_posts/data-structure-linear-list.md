---
title: 线性表 | 数据结构
tags:
  - 数据结构
  - C
categories: 基础
abbrlink: 986f7c16
date: 2021-04-25 22:00:00
---

线性表的定义和基本操作。

<!-- more -->

## 线性表的定义

线性表是具有**相同类型**的 n 个数据元素的**有限序列**。其中 n 为**表长**，当 n=0 时线性表是一个**空表**。

若用 L 命名线性表，则其一般表示为：L=(a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>i</sub>, a<sub>i+1</sub>, a<sub>n</sub>)

**位序**：表示第 i 个元素。从 1 开始，而数组下标是从 0 开始。

**表头元素**：第 1 个元素。

**表尾元素**：第 n 个元素，最后一个元素。

除了表头元素外，每个元素有且仅有一个**直接前驱**；除了表尾元素外，每个元素有且仅有一个**直接后继**。

## 线性表的基本操作

为什么要实现对数据结构的基本操作？

1. 团队合作编程，封装数据结构让团队能够很方便的使用。
2. 将常用的操作/运算封装成函数，避免重复工作，降低出错风险。

### 抽象的操作

**初始化表**：`InitList(&L)` 构造一个空的线性表 L，并分配内存空间。

**销毁操作**：`DestroyList(&L)` 销毁线性表，并释放其占用的内存空间。

**插入操作**： `ListInsert(&L, i, e)` 在表 L 中的第 i 个位置上插入指定元素 e。

**删除操作**：`ListDelete(&L, i, &e)` 删除表 L 中第 i 个位置的元素，并用 e 接受被删除的元素的值。

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
    ElemType data[MAX_SIZE];  // 用数组存放数据元素，ElemType 为指定的数据类型
    int length;               // 顺序表的当前长度
} SeqList;                    // 顺序表的类型定义
```

例如：

```c
#include <stdio.h>

// MAX_SIZE 顺序表最大容量
#define MAX_SIZE 10

// true 定义测试正确符号
const char true[] = "✔";
// false 定义测试错误符号
const char false[] = "✘";

// SqList 定义顺序表结构
typedef struct {
    int data[MAX_SIZE];
    int length;
} SeqList;

// 初始化顺序表 <L>
void InitList(SeqList *L) {
    // 只需要将顺序表初始长度修改为 0 即可
    // 不需要为所有数据元素设置默认值
    L->length = 0;
}

// ListInsert 在顺序表 <L> 的第 <i> 个位置插入指定元素 <e>
// 如果 i 的范围无效，返回 0；成功插入元素返回 1
int ListInsert(SeqList *L, int i, int e) {
    // 判断 i 的范围是否有效
    if (i < 1 || i > L->length + 1) {
        return 0;
    }
    // 判断当前存储空间是否已满
    if (L->length >= MAX_SIZE) {
        return 0;
    }
    // 将第 i 个位置后的元素及顺序后移
    for (int j = L->length; j >= i; j--) {
        L->data[j] = L->data[j - 1];
    }
    // 在位序 i 处添加指定元素
    L->data[i - 1] = e;
    // 顺序表的当前长度加 1
    L->length++;
    return 1;
}

// ListDelete 删除顺序表 <L> 的第 <i> 个元素，并用 <e> 返回删除元素的值
// 如果 i 的范围无效，返回 0；成功删除指定元素返回 1
int ListDelete(SeqList *L, int i, int *e) {
    // 判断 i 的范围是否有效
    if (i < 1 || i > L->length) {
        return 0;
    }
    // 将删除元素的值赋值给 e
    *e = L->data[i - 1];
    // 将第 i 个位置后的元素顺序前移
    for (int j = i; j < L->length; j++) {
        L->data[j - 1] = L->data[j];
    }
    // 顺序表当前长度减 1
    L->length--;
    return 1;
}

// GetElem 获取顺序表 <L> 中第 <i> 个元素，用 <e> 返回对应元素的值
// 如果 i 的范围无效，返回 0；成功获取指定元素返回 1
int GetElem(SeqList L, int i, int *e) {
    // 判断 i 的范围是否有效
    if (i < 1 || i > L.length) {
        return 0;
    }
    // 将第 i 个位置的元素赋值给 e
    *e = L.data[i - 1];
    return 1;
}

// LocateElem 在顺序表 <L> 中查找第一个元素值等于 <e> 的元素，并返回其位序
// 如果找到指定元素返回对应元素的位序，否则返回 0
int LocateElem(SeqList L, int e) {
    // 遍历整个数组
    for (int i = 0; i < L.length; i++) {
        // 判断当前位置的元素值和指定的元素是否相同
        // 相同直接返回对应位置
        if (L.data[i] == e) {
            return i + 1;
        }
    }
    return 0;
}

// Length 返回顺序表 <L> 的当前长度
int Length(SeqList L) {
    return L.length;
}

// PrintList 按前后顺序打印顺序表 <L> 的所有元素及其下标
void PrintList(SeqList L) {
    printf("Print List: [");
    for (int i = 0; i < L.length - 1; i++) {
        printf("%d, ", L.data[i]);
    }
    printf("%d]\n", L.data[L.length - 1]);
}

// Empty 判断顺序表 <L> 是否是空表，是则返回 1，否则返回 0
int Empty(SeqList L) {
    return L.length == 0;
}

int main() {
    // 声明顺序表变量
    SeqList sqList;
    int e, index, length;
    // 初始化顺序表
    InitList(&sqList);
    // 判断是否是空表
    if (Empty(sqList)) {
        printf("判断顺序表是否是空表 %s\n", true);
    } else {
        printf("判断顺序表是否是空表 %s\n", false);
    }
    // 向顺序表添加 5 个元素
    for (int i = 1; i < 6; i++) {
        int tmp = 10 * i;
        if (ListInsert(&sqList, i, tmp)) {
            printf("向顺序表中正确位序插入数据 %s\n", true);
        } else {
            printf("向顺序表中正确位序插入数据 %s\n", false);
        }
    }
    // 判断是否是空表
    if (!Empty(sqList)) {
        printf("判断顺序表是否是空表 %s\n", true);
    } else {
        printf("判断顺序表是否是空表 %s\n", false);
    }
    // 向顺序表第 7 个位置添加元素
    if (!ListInsert(&sqList, 7, 70)) {
        printf("向顺序表中不合理位序插入数据 %s\n", true);
    } else {
        printf("向顺序表中不合理位序插入数据 %s\n", false);
    }
    // 获取第 7 个位置的元素
    if (!GetElem(sqList, 7, &e)) {
        printf("获取顺序表中不合理位序的元素值 %s\n", true);
    } else {
        printf("获取顺序表中不合理位序的元素值 %s\n", false);
    }
    // 查找元素值为 20 的元素的位序
    index = LocateElem(sqList, 20);
    if (index == 2) {
        printf("查找顺序表中合理位序的元素的值 %s\n", true);
    } else {
        printf("查找顺序表中合理位序的元素的值 %s\n", false);
    }
    // 查找元素值为 80 的元素的位序
    if (!LocateElem(sqList, 80)) {
        printf("查找顺序表中不合理位序的元素的值 %s\n", true);
    } else {
        printf("查找顺序表中不合理位序的元素的值 %s\n", false);
    }
    // 删除顺序表第 8 个位置的元素
    if (!ListDelete(&sqList, 8, &e)) {
        printf("删除顺序表中不合理位序的元素 %s\n", true);
    } else {
        printf("删除顺序表中不合理位序的元素 %s\n", false);
    }
    // 查看顺序表已用长度
    length = Length(sqList);
    if (length == 5) {
        printf("获取顺序表的当前长度 %s\n", true);
    } else {
        printf("获取顺序表的当前长度 %s\n", false);
    }
    // 删除顺序表第 3 个位置的元素
    if (ListDelete(&sqList, 3, &e)) {
        printf("删除顺序表中合理位序的元素 %s\n", true);
    } else {
        printf("删除顺序表中合理位序的元素 %s\n", false);
    }
    // 继续添加 6 个元素。应该成功
    for (int i = 1; i < 7; ++i) {
        int tmp = 10 * i;
        if (ListInsert(&sqList, i, tmp)) {
            printf("向顺序表中正确位序插入数据 %s\n", true);
        } else {
            printf("向顺序表中正确位序插入数据 %s\n", false);
        }
    }
    // 当前顺序表已满，继续添加元素
    if (!ListInsert(&sqList, 2, 20)) {
        printf("向顺序表中错误位序插入数据 %s\n", true);
    } else {
        printf("向顺序表中错误位序插入数据 %s\n", false);
    }
    // 获取第 2 个位置的元素
    if (GetElem(sqList, 7, &e)) {
        printf("获取顺序表中合理位序的元素值 %s\n", true);
    } else {
        printf("获取顺序表中合理位序的元素值 %s\n", false);
    }
    // 打印顺序表
    PrintList(sqList);
    return 0;
}

/**
* Output:
* 判断顺序表是否是空表 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 判断顺序表是否是空表 ✔
* 向顺序表中不合理位序插入数据 ✔
* 获取顺序表中不合理位序的元素值 ✔
* 查找顺序表中合理位序的元素的值 ✔
* 查找顺序表中不合理位序的元素的值 ✔
* 删除顺序表中不合理位序的元素 ✔
* 获取顺序表的当前长度 ✔
* 删除顺序表中合理位序的元素 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中正确位序插入数据 ✔
* 向顺序表中错误位序插入数据 ✔
* 获取顺序表中合理位序的元素值 ✔
* Print List: [10, 20, 30, 40, 50, 60, 10, 20, 40, 50]
*/
```

#### 动态分配

和静态分配的区别在于当顺序表已满的时候再插入元素，静态分配返回错误，而动态分配重新分配数组，插入元素。

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
#include <stdio.h>
#include <stdlib.h>

// INIT_SIZE 顺序表最大容量
#define INIT_SIZE 10

// SqList 定义顺序表结构
typedef struct {
    int *data;
    int maxSize;
    int length;
} SeqList;

// 初始化顺序表 <L>
void InitList(SeqList *L) {
    // 使用 malloc 函数申请一片连续的存储空间
    L->data = (int *) malloc(INIT_SIZE * sizeof(int));
    L->maxSize = INIT_SIZE;
    L->length = 0;
}

// IncreaseSize 将顺序表 <L> 的长度增加 <len>
void IncreaseSize(SeqList *L, int len) {
    int *p = L->data;
    // 顺序表最大长度增加 len
    L->maxSize += len;
    L->data = (int *) malloc(L->maxSize * sizeof(int));
    // 将数据赋值到新区域
    for (int i = 0; i < L->length; i++) {
        L->data[i] = p[i];
    }
    // 释放原来的内存空间
    free(p);
}

// ListInsert 在顺序表 <L> 的第 <i> 个位置插入指定元素 <e>
// 如果 i 的范围无效，返回 0；成功插入元素返回 1
// 如果顺序表已满，则扩充数组空间
int ListInsert(SeqList *L, int i, int e) {
    // 判断 i 的范围是否有效
    if (i < 1 || i > L->length + 1) {
        return 0;
    }
    // 判断当前存储空间是否已满，已满则扩充空间
    if (L->length >= L->maxSize) {
        IncreaseSize(L, INIT_SIZE);
    }
    // 将第 i 个位置后的元素及顺序后移
    for (int j = L->length; j >= i; j--) {
        L->data[j] = L->data[j - 1];
    }
    // 在位序 i 处添加指定元素
    L->data[i - 1] = e;
    // 顺序表的当前长度加 1
    L->length++;
    return 1;
}
```

#### 顺序表的特点

1. 随机访问。可以在 O(1) 时间内找到第 i 个元素。
2. 存储密度高。每个节点只存储数据元素。
3. 扩展容量不方便。即便采用动态分配的方式实现，拓展长度的时间复杂度也比较高。
4. 插入、删除操作不方便，需要移动大量元素。

[^1]: [数据结构-王道考研](https://www.bilibili.com/video/BV1b7411N798)