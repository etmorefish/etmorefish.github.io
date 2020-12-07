---
title: python list, tuple的底层细节
tags:
  - Python
categories:
  - Python
top: false
date: 2020-08-10 14:34:04
---

## 列表和元组

列表和元组的区别是显然的：列表是动态的，其大小可以该标；

而元组是不可变的，一旦创建就不能修改。

### 实现细节

**在CPython中，列表被实现为长度可变的数组。**

从细节上看，Python中的列表是由**对其它对象的引用组成的连续数组**。指向这个数组的指针及其长度被保存在一个列表头结构中。这意味着，每次添加或删除一个元素时，由引用组成的数组需要该标大小（重新分配）。幸运的是，Python在创建这些数组时采用了指数过分配，所以并不是每次操作都需要改变数组的大小。但是，也因为这个原因添加或取出元素的平摊复杂度较低。

> 利用 list.insert(i,item) 方法在任意位置插入一个元素——复杂度O(N)
> 利用 list.pop(i) 或 list.remove(value) 删除一个元素——复杂度O(N)

可以采用时间复杂度来衡量：

index() O(1)
append O(1)
pop() O(1)
pop(i) O(n)
insert(i,item) O(n)
del operator O(n)
iteration O(n)
contains(in) O(n)
get slice[x:y] O(k)
del slice O(n)
set slice O(n+k)
reverse O(n)
concatenate O(k)
sort O(nlogn)
multiply O(nk)

## 

##### 列表和元组的区别是显然的：



list和tuple在c实现上是很相似的，对于元素数量大的时候，
都是一个数组指针，指针指向相应的对象，找不到tuple比list快的理由。
但对于小对象来说，tuple会有一个对象池，所以小的、重复的使用tuple还有益处的。

为什么要有tuple，还有很多的合理性。
实际情况中的确也有不少大小固定的列表结构，例如二维地理坐标等；
另外tuple也给元素天然地赋予了只读属性。



[参考https://blog.csdn.net/siyue0211/article/details/80560783](https://blog.csdn.net/siyue0211/article/details/80560783)