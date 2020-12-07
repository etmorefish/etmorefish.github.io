---
title: python小结(五)
tags:
  - Python
categories:
  - Python
top: false
date: 2020-06-25 14:34:04
---
# python小结(五)

##### json序列化时，默认中文会变成unicode，如何避免

```python
import json
json.dumps({'dd':'你好'}, ensure_ascii=False)                     # '{"dd": "你好"}'
```

##### python递归的最大层数？998

系统堆栈深度有一定限度，防止无限递归造成Python溢出崩溃

```python
#修改深度
import sys
sys.setrecursionlimit(1500)
```

##### 用一行代码实现数值交换：

```python
a = 1
b = 2
c=a;a=b;b=c
```

##### 文件操作时：xreadlines和readlines的区别？

```
read()会读取整个文件，将读取到底的文件内容放到一个字符串变量，返回str类型
readline()读取一行内容，放到一个字符串变量，返回str类型
readlines()读取文件所有内容，按行为单位放到一个列表中，返回list类型
xreadlines()返回一个生成器，来循环操作文件的每一行
```

##### *arg和**kwarg作用

```
def function(required_arg,*arg,**kwarg):    
	pass
```

*arg会把多出来的位置参数转化为tuple
**kwarg会把关键字参数(key=value)转化为dict

##### is和==的区别

```
对象：id(身份标识)、type(数据类型)和value(值)
is判断对象所有信息，==比较值
只有数值型和字符串型的情况下，a is b才为True，当a和b是tuple，list，dict或set型时，a is b为False
```

##### 简述Python的深浅拷贝以及应用场景？

```
浅复制：仅拷贝基本数据类型，字典 copy 方法和copy.copy()方法，也是浅复制；
深复制：拷贝数据类型和引用，计算机开辟一块新内存用于存放复制对象。
```

##### logging模块的作用？以及应用场景？

```
logging模块定义的函数和类为应用程序和库的开发实现了一个灵活的事件日志系统


```

##### 简述容器、生成器、迭代器、可迭代对象 以及应用场景？

容器：多元素组织的数据结构，可逐个迭代地获取，通常数据结构把元素存储在内存中（迭代器和生成器对象不放在内存）。
可迭代对象：很多容器都是可迭代对象，实现了iter方法，还有打开状态的files、sockets可以返回一个迭代器的对象，。
迭代器：带状态的对象，可以通过调用实现**iter**和**next**的对象都是迭代器，iter返回自身，next返回容器的下一个值。
生成器：生成器是一类特殊的迭代器，不需要iter和next方法，直接使用yield返回，生成器一定是迭代器，生成器以一种懒加载的模式生成值。可以利用更少地中间变量写流式代码，相比其它容器对象更加节约内存和CPU
生成器表达式：列表推导式，返回一个生成器对象。

##### 谈谈你对闭包的理解？

闭包使得局部变量在函数外被访问成为可能，函数作为函数的值返回，闭包本质上是一个特殊的函数，闭包将变量的值始终保存在函数中。闭包避免了使用全局变量，此外，闭包允许将函数与其所操作的某些数据（环境）关连起来。这一点与面向对象编程是非常类似的，在面向对象编程中，对象允许我们将某些数据（对象的属性）与一个或者多个方法相关联。当对象中只有一个方法时，这时使用闭包是更好的选择。所有函数都有一个 **closure**属性，如果这个函数是一个闭包的话，那么它返回的是一个由 cell 对象 组成的元组对象。cell 对象的cell_contents 属性就是闭包中的自由变量。

##### Python 垃圾回收机制？

72.魔法函数 **call**怎么使用?

73.如何判断一个对象是函数还是方法？

74.@classmethod 和@staticmethod 用法和区别

75.Python 中的接口如何实现？

76.Python 中的反射了解么?

77.metaclass 作用？以及应用场景？

78.hasattr() getattr() setattr()的用法

```python
# https://www.cnblogs.com/lizm166/p/10267858.html
class A():
    name = 'python'
    def func(self):
        return 'Hello world'

if hasattr(A, 'age'):
    print(getattr(A, 'age'))
else:
    setattr(A, 'age', 20)

getattr(A, 'age')
# 结果：20
```

79.请列举你知道的 Python 的魔法方法及用途。

80.如何知道一个 Python 对象的类型？

81.Python 的传参是传值还是传址？



