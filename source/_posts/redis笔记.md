---
title:  Redis 笔记
tags:
  - Redis
categories:
  - 数据库
top: false
date: 2020-07-30 19:16:05
---
# Redis笔记

## 常用命令

```bash
select 1 # 切换到数据库1,范围是0~15。redis只能有16个db，不同mysql（mysql的database可以有无数个）
help set # 查看set到帮助信息
save # 手动持久化
flushdb # 清空当前库
flushall #16个库的数据全删了
dbsize # 看看有多少个值info # 各个库的键值情况
keys * # 查看所有键，这是运维禁忌keys z*，keys k？ # 通配符匹配
exists key1 # 判断key1是否存在
move key1 2 # 将key1从当前数据库移动到2号数据库
expire key1 60 # 将key1设置为60秒后过期
ttl key1 # 查看key1还有多少秒过期
type key1 # 看看key1是什么类型
del key1 #删掉rename k1 k2 # 改名
```

### 字符串(string)

string类型是二进制安全的，意思是可以包含任何数据，比如jpg图片或者序列化的对象。字符串value最多是512M

```bash
增
set k1 'ddd' ex 3 # 设置3秒之后过期
setex k1 3 'ddd' # 同上，set with expire
set k1 'ddd' NX # 和字典的setdefault效果一样
setnx k1 ddd # 同上，set if not exist
set love 'ddd' XX # 只有love这个key存在时这条命令才生效
getset k1 fuck # 先get再set

mset apple 12000 xiaomi 2000 oppo 3300 # 批量设置，{'apple':12000,'xiaomi':2000,'oppo':3300}
msetnx apple 12000 xiaomi 2000 # 只要有一个键存在，全体跪

查
get k1 
strlen k1 # 返回k1字符串的长度，注意是字节长度（汉字是三个字节）

切片
getrange k1 0 -1 #切片
setrange k1 0 xxxxx # 这个注意，0代表设置字符的位置，多余的字符会覆盖掉后续已经存在的字符

数字加减
incr count # count为数字类型的字符串变量，count++
decr count # count--
incrby count 20 # count+=20
decyby count 20 # count-=20
```

### 哈希(hash)

value是一个小字典，常用于存储一个对象的详细信息。例如存储用户的具体信息等
若嵌套的话，API方法会帮你将列表，字典转化为字符串，无论递归到多深也不怕

```bash
增
hset info a 1 # {'info'：{'a':1}}
hmset info a 1 b 2 # {'info'：{'a':1,'b':2}},其实hset就可以批量设置
hsetnx info a 1 # set if not exist

查
hgetall info # 获取hash的键值对元组
hkeys info # 取出所有键
hvals info # 取出所有值

hget info a  # {'info'：{'a':1,'b':2}},取value里面a键对应的值
hmget info a b # 批量取

删
hdel info a # 删除info里面的a

数字操作
hincrby info age 2 # 将age对应的value加2
hincrbyfloat info price 2.5 将price对应的value加2.5

通配符匹配指定key 
hscan info 0 match e* # 0代表全局匹配
```

### 队列(list)

value是一个列表,底层其实是双向链表，有lpush，rpush
性能的话，由于是链表，头尾性能高，中间插入性能低

```bash
增
lpush li a b c # {'li':['a','b','c']}
rpush li a # right push
rpoplpush 源列表 目的列表 # 将源列表的右边的(rpop)弹出的值，lpush进新的列表

删
lpop # 左边弹出
rpop # 右边弹出
lrem li 2 3 # 删除2个'3'

查
lindex li 1 # 取出li[1]
llen li # 长度

改
lset li 0 ff # li[0]='ff'
linsert li before/after a aa # 在元素a之前(之后)插入aa，注意，这里用的不是索引值而是元素

切片
lrange li 0 -1 # 范围取值
ltrim key1 0 3 # 截取索引位置0～3多范围的值赋值给key1

数字操作
hincrby info age 2 # 将age对应的value加2
hincrbyfloat info price 2.5 将price对应的value加2.5

通配符匹配指定key 
hscan info 0 match e* # 0代表全局匹配
```

### 集合(set)

value是一个set

```bash
增
sadd s 1 2 2 3 3 4 # {'s':{'1','2','3','4'}}
smove s1 s2 val_of_s1 # 将s1中的val的val_of_s1移动到s2

查
smembers s # 取出s的所有值
scard s1 # 获得s1集合里面元素个数
sismember s1 2 # 判断2是否为s1的元素

删
srem s1 fuck # 删除s1中的fuck
srandmember s1 2 # 随机从s1删除2个元素
spop key # 随机删除一个元素

集合操作
sdiff s1 s2 #差集 ，即s1-s2 ，s1有的，s2没有
sinter s1 s2 #交集 
sunion s1 s2 #并集
```

### 有序集合(zset)

有序集合，按照指定的权重进行排序

```bash
增
zadd s1 60 v1 80 v2 100 v3 #数字是权重（计算机术语中score代表的是权重）

查
zcard s1 #返回val的数目
zcount s1 60 80 #统计权重60到80之间的数目
zrank s1 v3 #返回v3的下标,注意是类似数组的顺序
zrevrank s1 v3 # 逆序返回v3的下标
zscore s1 v3 #返回v3的权重值

zrange s1 0 -1  withscores # 显示权重
zrange s1 0 -1 # 只显示值，不显示权重。注意:0，-1是下标范围。不是像mysql limit一样的参数
zrevrange s1 0 -1 # 只显示值，不显示权重。注意:0，-1是下标范围。

zrevrangebyscore s1 90 20 # 逆向显示权重范围的，注意参数1要大于参数2
zrangebyscore s1 60 80 # 显示指定权重范围的
zrangebyscore s1 (60 (80 # '('为不包含
zrangebyscore s1 60 80 limit 2 2 #类似于mysql数据库

zrevrangebyscore s1 90 60 #由于是反转，权重是90到60

删
zrem s1 v3 #删除v3
```

## 持久化

既可以把redis理解为缓存技术，也可以理解为数据库，因为redis支持将内存中的数据周期性的写入磁盘或者把操作追加到记录文件中，这个过程称为redis的持久化。

redis支持两种方式的持久化，一种是快照方式（snapshotting）,也称RDB方式；

两一种是追加文件方式（append-only file），也称AOF方式。

RDB方式是redis默认的持久化方式。

**- RDB方式原理**
当redis需要做持久化时（执行SAVA或者BGSAVA命令，或者是达到配置条件时执行），redis会fork一个子进程，子进程将数据写到磁盘上一个临时RDB文件中，当子进程完成写临时文件后，将原来的RDB替换掉（默认文件名为dump.rdb）

**- RDB优缺点**
定时备份，Redis效率高，但是容易造成数据丢失，丢失的多少和备份策略有关，

例如：5分钟备份一次，但是第8分时宕机了，那么就丢失了后面的3分钟数据

**- AOF方式原理**
AOF就可以做到全程持久化，Redis每执行一个修改数据的命令，都会把这个命令添加到AOF文件中，当Redis重启时，将会读取AOF文件进行“重放”以恢复到 Redis关闭前的最后时刻。

**- AOF优缺点**

优点：AOF基本可以保证数据不丢失，数据完整性比rdb要高。
缺点： 1.AOF持久化文件会变的越来越大。例如我们调用incr test命令100次，文件中必须保存全部的100条命令，其实有99条都是多余的。
　　　2.频繁的IO和aof文件过大时的rewrite会带来系统波动，并且由于语句过多且不断变化，导致恢复数据速度慢于rdb，并且备份数据库可能会出bug。所以一般不单独使用（以防万一）

**- 如果一个系统里面，同时存在rdb和aof，它们是冲突还是协作？**
两者可以共存，先加载的是aof。如果aof错误，redis-server起不来

## 主从复制

主从复制主要用于容灾恢复（主机挂了，能迅速切换到从机，然后去修从机）和读写分离。
主从复制有延迟这个不可避免的缺点，但是不妨碍其成为流行的技术

**- 主从复制的特点
**一个主服务器可以有多个从服务器。不仅主服务器可以有从服务器， 从服务器也可以有自己的从服务器。

Redis 支持异步复制和部分复制（这两个特性从Redis 2.8开始），主从复制过程不会阻塞主服务器和从服务器。

Master Server是以非阻塞的方式为Slaves提供服务。所以在Master-Slave同步期间，客户端仍然可以提交查询或修改请求。Slave Server同样是以非阻塞的方式完成数据同步。在同步期间，如果有客户端提交查询请求，Redis则返回同步之前的数据。

一般是主写从读。如让多个从服务器处理只读命令，使用复制功能来让主服务器免于频繁的执行持久化操作。即只有主机可以写，从机不可以写



Master可以将数据保存操作交给Slaves完成，从而避免了在Master中要有独立的进程来完成此操作。

[参考](https://www.cnblogs.com/ziyide/p/9488564.html)