---
title: Mongodb 和 Redis 的区别
tags:
  - Mongodb
  - Redis
categories:
  - 数据库
top: false
date: 2020-07-30 19:16:05
---

# Mongodb 和 Redis 的区别

MongoDB和Redis都是NoSQL数据库，采用结构型数据存储，而MySQL、oracle等则属于传统的关系型数据库。

### NoSQL数据库与关系型数据库的优缺点**

- **关系型数据库**

优点：以完善的关系代数理论为基础,有严格的标准,支持事务ACID四性,借助索引机制可以实现高效的查询。

> 原子性、一致性、隔离性、持久性

缺点：可扩展性差，无法较好地支持海量数据存储，数据模型过于死板，事务机制影响了系统的整体性能，全文搜索功能较弱。

- **NoSQL数据库**

优点：数据之间无关系，易扩展。有非常高的读写性能，支持大量数据，性能高。有灵活的数据模型，无须事先为要存储的数据建立字段，随时可以存储自定义的数据格式。

缺点：复杂查询性能不高,一般都不能实现事务的强一致性。

### **redis与mongoDB的区别**

- **内存管理**

Redis 数据全部存在内存，定期写入磁盘，当内存不够时，可以选择指定的 LRU 算法删除数据。

MongoDB 数据会优先存于内存，当内存不够时，只将热点数据放入内存，其他数据存在磁盘。

*需要注意的是Redis 和mongoDB特别消耗内存，一般不建议将它们和别的服务部署在同一台服务器上。*

- **数据结构**

Redis 支持的数据结构丰富，包括hash、string、list、set、zset。

MongoDB 文件存储是BSON格式类似JSON，或自定义的二进制格式。数据结构比较单一，但是支持丰富的数据表达，索引，最类似关系型数据库，支持的查询语言非常丰富。

- **数据量和性能**

  mongodb与redis性能都很依赖内存的大小，mongodb 有丰富的数据表达、索引；最类似于关系数据库，支持丰富的查询语言，redis数据丰富，较少的IO ，这方面mongodb优势明显。

  mongodb对海量数据的访问效率提升，redis 较小数据量的性能及运算,这方面 mongodb性能优于redis .monbgodb 有mapredurce功能，提供数据分析，redis 没有 ，这方面 mongodb优于redis 。

  

当物理内存够用的时候，性能，redis>mongodb>mysql

数据量，mysql>mongodb>redis

*注意mongodb可以存储文件，适合存放大量的小文件，内置了GirdFS 的分布式文件系统。*

- **可靠性**

  mongodb不支持事物，靠客户端自身保证，redis支持事物，比较弱，仅能保证事物中的操作按顺序执行，这方面 redis优于mongodb。

mongodb从1.8版本后，采用binlog方式（MySQL同样采用该方式）支持持久化，增加可靠性；

Redis依赖快照进行持久化；AOF增强可靠性；增强可靠性的同时，影响访问性能。

可靠性上MongoDB优于Redis。



![img](https://upload-images.jianshu.io/upload_images/11422128-575cd92e76caedea.png?imageMogr2/auto-orient/strip|imageView2/2/w/491/format/webp)

**171. mysql 索引是怎么实现的？**

索引是满足某种特定查找算法的数据结构，而这些数据结构会以某种方式指向数据，从而实现高效查找数据。



具体来说 MySQL 中的索引，不同的数据引擎实现有所不同，但目前主流的数据库引擎的索引都是 B+ 树实现的，B+ 树的搜索效率，可以到达二分法的性能，找到数据区域之后就找到了完整的数据结构了，所有索引的性能也是更好的。

**175. 说一下 mysql 的行锁和表锁？**

MyISAM 只支持表锁，InnoDB 支持表锁和行锁，默认为行锁。



- 表级锁：开销小，加锁快，不会出现死锁。锁定粒度大，发生锁冲突的概率最高，并发量最低。
- 行级锁：开销大，加锁慢，会出现死锁。锁力度小，发生锁冲突的概率小，并发度最高。

**178. 如何做 mysql 的性能优化？**

- 为搜索字段创建索引。
- 避免使用 select *，列出需要查询的字段。
- 垂直分割分表。
- 选择正确的存储引擎。

[引用mysql](https://mp.weixin.qq.com/s?__biz=MzIwMTY0NDU3Nw==&mid=2651938577&idx=2&sn=4943c30f0c0f7be2184d0275378085fa&chksm=8d0f305fba78b9493e45048af00f454e4352cb94181b637b1f6f93f94fa385ba3a6f092c0b25&mpshare=1&scene=1&srcid=0813QGYaBHVpqL3TH9CCNevT&sharer_sharetime=1597328200226&sharer_shareid=b59d477b5eab6b4160165bc8bf143bf9&key=281d398fa0af70c927b9b1f6d21582dafbb4795fb3495b5450a51a8f51a633404b6b8bf6337cd02db79ce62058ba7e2b18ede46e33e0636b21031f30a0783998c8db6529ae24083357f7024841e7f5ea&ascene=1&uin=MTY1NTEzNDEwMw%3D%3D&devicetype=Windows+XP&version=62060841&lang=zh_CN&exportkey=AYA9K6fq8gpv9k%2Bw3lHAlyU%3D&pass_ticket=KF3ZYQGe69eA2wf6qHzvVf2iikdbBS4yxBkgWqlJFBWQx1nJTf21s0CRDuypaA8Q)

