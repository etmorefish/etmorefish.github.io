---
title: python小结(一)
tags:
  - Python
categories:
  - Python
top: false
date: 2020-06-20 14:34:04
---
# python小结(一)

## 1. 列出几个常用Python标准库

```bash
os, math, random, time, datetime,re, copy, csv, logging, json
```
## 2. python 内建数据类型有哪些

1. **Booleans［布尔型］** 或为 `True［真］` 或为 `False［假］`。
2. **Numbers［数值型］** 可以是 Integers［整数］（`1` 和 `2`）、Floats［浮点数］（`1.1` 和 `1.2`）、Fractions［分数］（`1/2` 和 `2/3`）；甚至是 [Complex Number［复数］](http://en.wikipedia.org/wiki/Complex_number)。
3. **Strings［字符串型］** 是 Unicode 字符序列，*例如：* 一份 html 文档。
4. **Bytes［字节］** 和 **Byte Arrays［字节数组］**， *例如:* 一份 jpeg 图像文件。
5. **Lists［列表］** 是值的有序序列。
6. **Tuples［元组］** 是有序而不可变的值序列。
7. **Sets［集合］** 是装满无序值的包裹。
8. **Dictionaries［字典］** 是键值对的无序包裹。

## 3. 简述 with 方法打开处理文件帮我们做了什么

```python
file = open('a.txt','w')
try:
    file.write('test')
except:
    pass
finally:
    file.close()
# with方法相当于以上代码finally中的file.close()操作
```

## 4. 列出 Python 中可变数据类型和不可变数据类型，为什么
> 不可变数据：Number（数字）、String（字符串）、Tuple（元组）；
> 可变数据：List（列表）、Dictionary（字典）、Sets（集合）

|          | list | tuple | set          | dict               |
| :------: | ---- | ----- | ------------ | ------------------ |
| 可否读写 | 读写 | 只读  | 读写         | 读写               |
| 可否重复 | 是   | 是    | 否           | 是                 |
| 存储方式 | 值   | 值    | 键(不能重复) | 键值对(键不能重复) |
| 是否有序 | 有序 | 有序  | 无序         | 无序，自动正序     |
|          |      |       |              |                    |



## 5. Python 获取当前日期

```python
import time
now = time.strftime("%Y-%m-%d %H:%M:%S %A")
print(now)
```

## 6. 统计字符串每个单词出现的次数

```python
def count(str):
    count_words = str.lower().split()
    count_word = {}
    for word in count_words:
        if word not in count_word.keys():
            count_word[word] = 1
        else:
            count_word[word] += 1
    return count_word

print(count('I can because i think i can'))
# {'i': 3, 'can': 2, 'because': 1, 'think': 1}

# 方法二
# from collections import Counter

# str = 'I can because i think i can'
# counts = Counter(str.lower().split())
# print(counts, type(counts), help(counts))
```



## 7. 用 Python 删除文件和用 Linux 命令删除文件的方法

```python
import os
# python删除文件
os.remove(path)
# python删除目录
os.removedirs(path)
```

```bash
rm [选项] 文件

选项说明：
-f　　　　-force　　　　　　忽略不存在的文件，强制删除，无任何提示
-i　　　　--interactive　　　 进行交互式地删除
-r | -R　　--recursive　　　  递归式地删除列出的目录下的所有目录和文件
-v　　　   --verbose　　　　详细显示进行的步骤
```

##  8. 举例说明异常模块中 try except else finally 的相关意义

```python
try:
    ------code-----
except Exception as e: # 抛出异常之后将会执行
    print(e)
else:  # 没有异常将会执行
    print('no Exception')
finally:  # 有没有异常都会执行
    print('execute is finish')
```

## 9. 遇到报错如何处理

```Python
查看报错行和错误类型，debug找出问题所在进行处理。
常见错误类型有以下几种：
1. TypeError: cannot concatenate 'str' and 'int' objects  
    不能连接str和int对象
2. IndexError: list index out of range
    索引错误:列表索引超出范围
3. IndentationError: expected an indented block
    缩进错误:预期一个缩进快(意思需要一个缩进块)
4. IndentationError: unexpected indent
    缩进错误:意外缩进(这里不应缩进)
5. TypeError: 'tuple' object does not support item assignment
    类型错误：元组对象不支持元素值重新分配，也就是不能尝试去修改元组中的任一个元素的值
6. SyntaxError: invalid syntax
    语法错误 非法的语法
7. AttributeError: 'tuple' object has no attribute 'remove'
    attribute 属性 object对象
8. KeyError: 'XXX'
    key键错误，没有指定的键值'XXX'
```

## 10. 在浏览器输入 URL，按下回车后的流程

> 1. URL 解析
> 2. DNS 查询
> 3. TCP 连接
> 4. 处理请求
> 5. 接受响应
> 6. 渲染页面

	1. 浏览器向 DNS 服务器请求解析该 URL 中域名对应的 IP 地址；
	2. 解析出 IP 地址之后，根据该 IP 地址和端口，和服务器建立 TCP 连接；
	3. 浏览器发出读取文件(URL 中域名和后面部分对应的文件)的HTTP 请求，该请求报文作为TCP 三次握手的第三个报文的数据发送给服务器；
	4. 服务器对浏览器的请求作出响应，并把对应的html文本发送给浏览器；
	5. 释放 TCP连接；
	6. 浏览器将html文本渲染显示内容。
1、首先进行缓存查找、如果找不到就进行DNS解析，解析出对应的IP地址

2、建立TCP连接，也就是三次握手

3、客户端向服务器发送http请求（协议版本号、目标主机地址、请求资源目录等）

4、服务器处理请求（对收到的http请求进行解析，做出相应的处理（包括：响应类型、响应描述信息、响应结果等））

5、浏览器解析网页（对收到的响应进行解析）

6、保持或释放连接

##### ***\*小结TCP与UDP的区别：\****

1.基于连接与无连接； 
2.对系统资源的要求（TCP较多，UDP少）； 
3.UDP程序结构较简单； 
4.流模式与数据报模式 ；
5.TCP保证数据正确性，UDP可能丢包，TCP保证数据顺序，UDP不保证。

三次握手

![三次握手](https://i.loli.net/2020/08/18/ty39nKZ6HzuT4vN.png)

四次挥手

![四次挥手](https://i.loli.net/2020/08/18/hSQbi5rINZc4KoO.png)