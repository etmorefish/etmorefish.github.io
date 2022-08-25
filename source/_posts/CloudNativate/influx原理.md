---
title: InfluxDB存储原理
tags:
  - k8s
  - influxdb
  - benchmark
  - prometheus
categories:
  - tsdb
top: false
date: 2022-07-27 18:33:18
---

# **InfluxDB存储原理**

InfluxDB的存储结构树是时间结构合并树（Time-Structured Merge Tree，TSM），它是由日志结构化合并树（Log-Structured Merge Tree，LSM），根据实际需求变化而来的。

**LSM树 **

LSM树包含三部分：Memtable，Immutable和SSTable。MemTable是内存中的数据结构，用于保存最近产生的数据，并按照Key有序地组织数据。内存并不是可靠存储，若断电就会丢失数据，因此通常会使用预写式日志(Write-ahead logging，WAL)的方式来保证数据的可靠性。

![lsm tree](https://s2.loli.net/2022/08/24/6R4mKYCgjaIE5AB.png)

 **TSM存储引擎**

TSM存储引擎主要包括四部分:Cache，WAL，TSM File，Compactor。下图中shard与TSM引擎主要部分放在一起，但其实shard在是TSM存储引擎之上的一个概念。在 InfluxDB 中按照数据产生的时间范围，会创建不同的shard分组，每个 shard 都有本身的 cache、wal、tsm file 以及 compactor。
![tsm engine](https://s2.loli.net/2022/08/24/ATmW1QMznoFqUdj.png)

整个数据的写入流程简化为 3 个步骤：

1. 先写入 WAL

2. 然后写入 Cache

3. 最终持久化为 TSM File

   ![shard](https://s2.loli.net/2022/08/24/7Zbqemzhxyf1aO4.jpg)

 WAL

　预写日志`Write-Ahead-Log`是一种常见的提高数据库优化手段，能够在保证数据安全的同时，提升系统的写入性能。
　InfluxDB WAL 由一组定长的 segement 文件构成，每个文件大小约为 10MB。这些 segment 文件只允许追加，不允许修改。

 Cache

　Cache 是 WAL 的一个内存快照，保证 WAL 中的数据对用户实时可见。
　当 Cache 空闲或者过满时，对应的 WAL 将被压缩并转换为 TSM，最终释放内存空间。
　每次重启时会根据 WAL 重新构造 Cache。

 TSM File

　TSM 是一组存储在磁盘上的外存索引文件，细节将在后续进行介绍。

　它们之间的关系可以简单描述为：

- Cache = WAL
- Cache + TSM = 完整的数据