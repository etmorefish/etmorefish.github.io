---
title: python小结(六)
tags:
  - Python
categories:
  - Python
top: false
date: 2020-06-26 14:34:04
---
# python小结(六)

### 正则

```python
# 邮箱
'^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+&'
```



### 排序算法

###### 冒泡排序 (O(n2))

```
def bubble_sort(li):
    for i in range(len(li)-1):  #第i趟
        exchange = False
        for j in range(len(li)-i-1):
            if li[j] > li[j+1]:
                li[j], li[j+1] = li[j+1], li[j]
                exchange = True
        if not exchange:
            return
```

###### 选择排序 (O(n2))

```python
def select_sort_simple(li):
    li_new = []
    for i in range(len(li)):
        min_val = min(li)
        li_new.append(min_val)
        li.remove(min_val)
    return li_new


def select_sort(li):
    for i in range(len(li)-1): # i是第几趟
        min_loc = i
        for j in range(i+1, len(li)):
            if li[j] < li[min_loc]:
                min_loc = j
        li[i], li[min_loc] = li[min_loc], li[i]
    return li
```

###### 快速排序 (O(nlogn))

```python
def partition(li, left, right):
    tmp = li[left]
    while left < right:
        while left < right and li[right] >= tmp: #从右面找比tmp小的数
            right -= 1      # 往左走一步
        li[left] = li[right] #把右边的值写到左边空位上
        # print(li, 'right')
        while left < right and li[left] <= tmp:
            left += 1
        li[right] = li[left] #把左边的值写到右边空位上
        # print(li, 'left')
    li[left] = tmp      # 把tmp归位
    return left


def _quick_sort(li, left, right):
    if left<right:  # 至少两个元素
        mid = partition(li, left, right)
        _quick_sort(li, left, mid-1)
        _quick_sort(li, mid+1, right)
    return li

# 方案二
def quick_sorts(li):
    if len(li) < 2:
        return li
    else:
        pivot = li[0]
        less = [i for i in li[1:] if i < pivot]
        greater = [j for j in li[1:] if j > pivot]
        return quick_sorts(less) + [pivot] + quick_sorts(greater)
```

##### 查找

###### 顺序查找

```python
def linear_search(li, val):
    for ind, v in enumerate(li):
        if v == val:
            return ind
    else:
        return None
```

###### 二分查找

```python
def binary_search(li, val):
    left = 0
    right = len(li) - 1
    while left <= right:           # 候选区有值
        mid = (left + right) // 2
        if li[mid] == val:
            return mid
        else li[mid] > val:      # 带查找的值在mid左侧
            right = mid - 1
        else :                  # 带查找的值在mid右侧
            left = mid + 1
    else:
        return None
```

