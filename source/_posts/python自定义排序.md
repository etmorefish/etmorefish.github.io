---
title: Python自定义排序
tags:
  - Python
categories:
  - Python
top: false
date: 2020-08-20 23:34:04
---

# Python自定义排序

`functools.``cmp_to_key`(*func*)

将(旧式的)比较函数转换为新式的 [key function](https://docs.python.org/zh-cn/3/glossary.html#term-key-function) . 在类似于 [`sorted()`](https://docs.python.org/zh-cn/3/library/functions.html#sorted) ， [`min()`](https://docs.python.org/zh-cn/3/library/functions.html#min) ， [`max()`](https://docs.python.org/zh-cn/3/library/functions.html#max) ， [`heapq.nlargest()`](https://docs.python.org/zh-cn/3/library/heapq.html#heapq.nlargest) ， [`heapq.nsmallest()`](https://docs.python.org/zh-cn/3/library/heapq.html#heapq.nsmallest) ， [`itertools.groupby()`](https://docs.python.org/zh-cn/3/library/itertools.html#itertools.groupby) 等函数的 key 参数中使用。此函数主要用作将 Python 2 程序转换至新版的转换工具，以保持对比较函数的兼容。

比较函数意为一个可调用对象，该对象接受两个参数并比较它们，结果为小于则返回一个负数，相等则返回零，大于则返回一个正数。key function则是一个接受一个参数，并返回另一个用以排序的值的可调用对象。

示例:

```python
sorted(iterable, key=cmp_to_key(locale.strcoll))  # locale-aware sort order
```

## 自定义排序示例

现在定义整数的比较规则，对整数a和b进行组合，组成ab和ba的形式。如果ab>ba,说明a优先级比较高，a应该比较大。针对这个问题排序不能够直接提取出一个key，那么就只能使用cmp_to_key函数进行转换。

```python
def cmp(a,b):
    str1 = str(a)+str(b)
    str2 = str(b)+str(a)
    if str1 > str2:
        return 1
    elif str1 < str2:
        return -1
    else:
        return 0

if __name__ == '__main__':
    nums = [1,3,20,15]
    nums.sort(key=functools.cmp_to_key(cmp))
    print(nums)         # [1, 15, 20, 3]
```

源码

```python
def cmp_to_key(mycmp):
    """Convert a cmp= function into a key= function"""
    class K(object):
        __slots__ = ['obj']
        def __init__(self, obj):
            self.obj = obj
        def __lt__(self, other):
            return mycmp(self.obj, other.obj) < 0
        def __gt__(self, other):
            return mycmp(self.obj, other.obj) > 0
        def __eq__(self, other):
            return mycmp(self.obj, other.obj) == 0
        def __le__(self, other):
            return mycmp(self.obj, other.obj) <= 0
        def __ge__(self, other):
            return mycmp(self.obj, other.obj) >= 0
        __hash__ = None
    return K
```

在函数内部创建了一个class,并且返回了这个class,在这个class中调用了传入的cmp函数进行了运算符重载。这样使得两个class的对象就可以进行比较了。在sort函数中，如果传入了key函数，它会在比较两个对象的时候，将两个对象作为参数传到key函数里面，然后对key函数的结果进行比较，从而完成了排序。当这里返回一个class的时候，这个key函数实际上就是class的构造函数，实际上就是将要比较的对象作为参数创建了两个K的对象，这两个对象进行过运算符重载，是可以比较的，比较的规则就是传入的cmp所定义的规则。这样就实现了自定义排序规则了。

## 例：拼接最大数字问题

有n个非非负整数,将其按照字符串串拼接的方方式拼接为一一个整数。
如何拼接可以使得得到的整数最大大?
例例:32,94,128,1286,6,71可以拼接除的最大大整数为
94716321286128

```python
from functools import cmp_to_key

li = [32, 94, 128, 1286, 6, 71]


def xy_cmp(x, y):
    if x + y < y + x:
        return 1
    elif x + y > y + x:
        return -1
    else:
        return 0


def numer_join(li):
    li = list(map(str, li))
    print(li)
    li.sort(key=cmp_to_key(xy_cmp))
    print(li)
    return ''.join(li)


print(numer_join(li))


['32', '94', '128', '1286', '6', '71']
['94', '71', '6', '32', '1286', '128']
94716321286128
```

