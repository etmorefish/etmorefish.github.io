---
title: python小结(四)
tags:
  - Python
categories:
  - Python
top: false
date: 2020-06-24 14:34:04
---
# python小结(四)

## 列表

### 1.已知 AList = [1,2,3,1,2],对 AList 列表元素去重，写出具体过程。

```python
AList=[1,2,3,1,2]
BList=[]
for i in AList:
    if i not in BList:
        BList.append(i)
print(BList)
```



### 2.如何实现 "1,2,3" 变成 ["1","2","3"]

```python
In [163]: a = '1,2,3'                                                                  
In [164]: a.split(',')                                                                 
Out[164]: ['1', '2', '3']

```



### 3.给定两个 list，A 和 B，找出相同元素和不同元素

```python
In [165]: a = [1,2,3 ,5]                                                               

In [166]: b = [3, 5, 7, 9] 

In [178]: set(set(a)&set(b))                                                           
Out[178]: {3, 5}

In [179]: set(set(a)^set(b))                                                           
Out[179]: {1, 2, 7, 9}

# 使用列表推导式    
In [180]: [x for x in a if x in b]                                                     
Out[180]: [3, 5]

```



### 4. [[1,2],[3,4],[5,6]]一行代码展开该列表，得出[1,2,3,4,5,6]

```python
a= [[1,2],[3,4],[5,6]]
# [j for i in a for j in i]
list=[]
for i in  a:
    for j in i:
        list.append(j)
print(list)
```



### 5.合并列表[1,5,7,9]和[2,2,6,8]

```
In [194]: a = [1, 5, 7, 9]                                                             

In [195]: b = [2, 4, 6, 8]

In [196]: a.extend(b)                                                                  

In [197]: a                                                                            
Out[197]: [1, 5, 7, 9, 2, 4, 6, 8]

```



### 6.如何打乱一个列表的元素？

```
In [201]: import random                                                                

In [202]: a = list(range(10))                                                          

In [203]: a                                                                            
Out[203]: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

In [204]: random.shuffle(a)                                                            

In [205]: a                                                                            
Out[205]: [9, 6, 1, 3, 0, 2, 5, 4, 7, 8]

```

## 字典

### 7.字典操作中 del 和 pop 有什么区别

```
dict.pop(key[, default])
如果字典中存在键，则dict.pop（）从字典中删除具有给定键的元素并返回其值。
如果字典中不存在给定的键，则它将返回给定的默认值。
如果字典中不存在给定键，并且没有将默认值传递给pop（），它将抛出KeyError

del key          # 删除键
dict.clear()     # 清空字典
del dict         # 删除字典
```



### 8.按照字典的内的值进行排序

```pythob
In [237]: z = {'a':24, 's':52, 'd':12, 'f':33}  

In [240]: sorted(z.keys())                                                             
Out[240]: ['a', 'd', 'f', 's']

In [239]: sorted(z.items(), key=lambda x:x[1])                                         
Out[239]: [('d', 12), ('a', 24), ('f', 33), ('s', 52)]

```



### 9.请合并下面两个字典 a = {"A":1,"B":2},b = {"C":3,"D":4}

```python
a.update(b)
```



### 10.如何使用生成式的方式生成一个字典，写一段功能代码。

```python
a = {'A': 1, 'B': 2, 'C': 3, 'D': 4}
print({k:v for k,v in a.items()})                                            
# {'A': 1, 'B': 2, 'C': 3, 'D': 4}

```



### 11.如何把元组("a","b")和元组(1,2)，变为字典{"a":1,"b":2}

```python
a = ("a","b")
b = (1,2)
dict(zip(a, b))
# {"a":1,"b":2}
```

### 如何实现[‘1’,’2’,’3’]变成[1,2,3] ?

```python
list(int(i) for i in ['1','2'，'3'])
```



