---
title: python collections模块的高效数据类型
date: 2022-01-25 09:43:19
tags:
  - Python
  - Collections
categories:
  - Python
top: false
---

# python collections模块的高效数据类型

> python的collections是用于存储数据集合(比如列表list, 字典dict, 元组tuple和集合set)的容器。这些容器内置在Python中，可以直接使用。该collections模块提供了额外的，高性能的数据类型，可以增强你的代码，使事情变得更清洁，更容易。

## Counter
Counter()是字典对象的子类。Counter()可接收一个可迭代遍历的对象（例如字符串、列表或元组）作为参数，并返回计数器字典。字典的键将是可遍历对象中的唯一元素，每个键的值将是可迭代对象中的每个唯一元素对应的计数。

```python
from collections import Counter

lst = [1, 2, 3, 3, 2, 1, 1, 1, 2, 2, 3, 1, 2, 1, 1]
counter = Counter(lst)

print(counter) # Conter ({1：7，2：2：5，3：3})

print(counter[1]) # 返回7。1的数量有7个
```

### most_common
Counter对象最有用的功能是most_common()函数。将其应用于Counter对象时，它将返回N个最常见元素及其计数的列表，按从最常见到最不常见的顺序排列。

```python

lst = [1, 2, 3, 3, 2, 1, 1, 1, 2, 2, 3, 1, 2, 1, 1]
counter = Counter(lst)
print(counter.most_common(2)) # 返回最常见两个元素及其计数 
# [（1，7），（2，5）]
```

### defaultdict
defaultdict工作起来完全像一个普通的Python字典，但它有额外的奖励。当您试图访问一个不存在的键，它不会引发错误，而是使用不存在的键创建新的key，其对应的默认值是根据创建defaultdict对象时作为参数传递的数据类型自动设置的。

```python

from collections import defaultdict

names_dict = defaultdict(int)
names_dict["Bob"] = 1
names_dict["Katie"] = 2
sara_number = names_dict["Sara"]

print(names_dict)
# defaultdict(<class 'int'>, {'Bob': 1, 'Katie': 2, 'Sara': 0})
```
> 在普通字典中，这将引发错误。使用defaultdict后不再报错，而是自动以"Sara"创建一个新键key，其初始化值为0，是因为我们指定了int数据类型作为初始化值。

如果要将设置列表类型数据做为一个key的初始化值，我们只需设置`names_dict = defaultdict(list)`，此时“Sara”将使用空列表初始化[]。此时打印结果如下所示：

`defaultdict(<class 'int'>, {'Bob': 1, 'Katie': 2, 'Sara': []})`

### deque
deque队列是在计算机科学里最基本的数据结构，遵循先入先出（FIFO）的原理。简单来说，这意味着添加到队列中的第一个对象也必须是要删除的第一个对象。我们只能在队列的前面插入内容，而只能从队列的后面删除内容，而队列中间没有任何动作。

collections模块提供的deque对象是能实现队列数据结构的优化版本。该功能的主要特色是能够保持队列的大小，即如果将队列的最大长度设置为10，则将deque根据FIFO原理添加和删除元素以保持最大长度始终为10。这是到目前为止，Python中队列的最佳实现。

```python
from collections import deque

my_queue = deque(maxlen=10)

for i in range(10):
    my_queue.append(i+1)

print(my_queue)

# 我们首先初始化deque，指定我们希望它始终保持最大长度为10。
# 其次，当我们通过循环将值插入到队列中时。
# 注意，填充队列的功能与使用常规Python列表的方式完全相同。
# 最后，我们打印出结果。

# deque([1, 2, 3, 4, 5, 6, 7, 8, 9, 10], maxlen=10)

# 由于队列中有一个maxlen=10，并且循环中添加了10个元素，
# 因此队列中包含了从1到10的所有数字。
# 现在，让我们看看添加更多数字时会发生什么。

for i in range(10, 15):
    my_queue.append(i+1)

print(my_queue)
# 在上面的代码中，我们在队列中添加了另外5个元素，数字从11到15。
# 但是我们的队列只有一个，且maxlen=10。
# 因此必须删除一些元素，才能插入新的元素。
# 由于队列必须遵循FIFO原则，
# 因此它将删除最先插入队列中的前5个元素，即[1、2、3、4、5]。
# 打印语句的结果如下：

# deque([6, 7, 8, 9, 10, 11, 12, 13, 14, 15], maxlen=10)
```

### namedtuple
在Python中创建常规元组时，其元素是通用的且未命名，这迫使您记住每个元组元素的确切索引。可以使用具名元组namedtuple来解决这个问题。

该namedtuple()返回与用于所述元组中的每个位置和一个通用名固定名称的元组namedtuple对象。要使用namedtuple，请先为其创建一个模板。下面的代码创建一个namedtuple名为Person的模板，其属性为name，age和job。

```python

from collections import namedtuple

Person = namedtuple('Person', 'name age job')
# 创建模板后，您可以使用它来创建namedtuple对象。
# 让我们使用Person模板为2个人创建2个namedtuple对象，并打印它们。

Person = namedtuple('Person', 'name age job')

Mike = Person(name='Mike', age=30, job='Data Scientist')
Kate = Person(name="Kate", age=28, job='Project Manager')

print(Mike)
print(Kate)
# 上面的代码非常简单。我们使用namedtuple 模板的所有属性来初始化“人员” ，
# 以后可以直接使用Mike或Kate使用元组元素，
# 而不用再使用索引了。上面的打印语句将给出以下结果：

Person(name='Mike', age=30, job='Data Scientist')
Person(name='Kate', age=28, job='Project Manager')
# 因此，namedtuple能够更容易地使用，更合适元组对象的组织，可读性也更强。
```

### OrderDict

Python的字典dict是无序的。如果先键值A先插入字典，键值B后插入字典，但是当你打印字典的Keys列表时，你会发现B可能在A的前面。对于无序字典，每次打印字典时每次显示元素的顺序都不一样。如果你的Python版本较老，需要借助collections模块提供的OrderedDict实现有序字典。

OrderedDict类似于正常的字典，只是它记住了元素插入的顺序。当对有序的词字典上迭代时，返回元素的顺序是按第一次添加元素的顺序进行。当元素删除时，排好序的词典保持着排序的顺序；但是当新元素添加时，就会被添加到末尾。

```python

dd = {'banana': 3, 'apple':4, 'pear': 1, 'orange': 2}
#按key排序
od_by_key = collections.OrderedDict(sorted(dd.items(), key=lambda t: t[0]))
print(od_by_key) 
#按照value排序
od_by_value = collections.OrderedDict(sorted(dd.items(),key=lambda t:t[1]))
print(od_by_value) 
#输出
OrderedDict([('apple', 4), ('banana', 3), ('orange', 2), ('pear', 1)])
OrderedDict([('pear', 1), ('orange', 2), ('banana', 3), ('apple', 4)])
```