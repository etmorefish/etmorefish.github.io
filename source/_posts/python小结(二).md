---
title: python小结(二)
tags:
  - Python
categories:
  - Python
top: false
date: 2020-06-21 14:34:04
---
# python小结(二)

# 语言特性和编码规范

## 1. 简述解释型和编译型编程语言

```
解释型:就是边解释边执行
编译性:编译后再执行
```

## 2. Python3 和 2 之间的区别

```python
1.print函数:（Python3中print为一个函数，必须用括号括起来；Python2中print为class）
2.通过input()解析用户的输入：（Python3中input得到的为str；Python2的input的到的为int型，Python2的raw_input得到的为str类型）
3.整数：Python3中/表示真除，%表示取余，//结果取整；Python2中带上小数点/表示真除，%表示取余，//结果取整
4.xrange模块改为range
5.比较操作符区别：Python2中任意两个对象都可以比较，Python3中只有同一数据类型的对象可以比较
6.编码：Python2中使用 ASCII 码作为默认编码方式，Python3默认使用utf-8编码
```

## 3. range和 xrange 的区别

```python
1.range返回的是一个list对象，而xrange返回的是一个生成器对象(xrange object)。
2.xrange则不会直接生成一个list，而是每次调用返回其中的一个值，内存空间使用极少，因而性能非常好。python3 取消了xrange（）函数
```

## 4. 什么是PEP8

```python
《Python Enhancement Proposal #8》（8 号 Python 增强提案）又叫 PEP8，他针对的 Python 代码格式而编订的风格指南。
1.缩进。4个空格的缩进（编辑器都可以完成此功能），不使用Tab，更不能混合使用Tab和空格。
2.每行最大长度79，换行可以使用反斜杠，最好使用圆括号。换行点要在操作符的后边敲回车。
3.类和top-level函数定义之间空两行；类中的方法定义之间空一行；函数内逻辑无关段落之间空一行；其他地方尽量不要再空行。
4.模块导入的顺序：按标准、三方和自己编写顺序依次导入，之间空一行。
5.不要在一句import中写多个库，比如import os, sys不推荐
6.避免不必要的空格
7.注释必须要有
8.函数命名要遵循规范
9.尽可能使用‘is’‘is not’取代‘==’，比如if x is not None 要优于if x。
10.使用基于类的异常，每个模块或包都有自己的异常类，此异常类继承自Exception。
11.异常中try的代码尽可能少。
```

# 5. 类型注释

```python
def greeting(name: str) -> str:
    return 'Hello ' + name

# python3.6后增加了类型注解
a: int = 123
b: str = 'hello'

# 更进一步，如果你需要指明一个全部由整数组成的列表：
from typing import List
l: List[int] = [1, 2, 3]
```

> 在函数 `greeting` 中，参数 `name` 预期是 [`str`](https://docs.python.org/zh-cn/3/library/stdtypes.html#str) 类型，并且返回 [`str`](https://docs.python.org/zh-cn/3/library/stdtypes.html#str) 类型。子类型允许作为参数。