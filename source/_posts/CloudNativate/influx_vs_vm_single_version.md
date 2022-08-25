---
title: influx_vs_victoriametrics_single_test_report 
tags:
  - k8s
  - influxdb
  - victoriametrics
  - benchmark
  - prometheus
categories:
  - tsdb
top: false
date: 2022-07-25 18:33:18
---
# influx_vs_victoriametrics_single_test_report

## 环境

- **Prometheus**: 178.104.163.111 2c4g

- **k8s集群**：178.104.163.88

- **Node**： 178.104.163.175	 2c6g


  > influxdb, victoriametrics 均安装最新版本作为本次测试


**架构图**

![image-20220725164911617](https://s2.loli.net/2022/08/24/RItj6TpcVk9Nv5a.png)

## 部署方式

[各组件部署方式](./single_version.md)

## 测试结果

> 运行时间两天

```sh
# 磁盘本地存储
[root@vm-3 ~]# du -sh vm
221M    vm
[root@vm-3 ~]# du -sh /var/lib/influxdb2/
1.6G    /var/lib/influxdb2/
```

![image-20220725132539769](https://s2.loli.net/2022/08/24/M2XBdumLibYhTcG.png)



- CPU

  ![image-20220725133149273](https://s2.loli.net/2022/08/24/6DfW4goZSQG5umz.png)

- Memory

  ![image-20220725133709846](https://s2.loli.net/2022/08/24/yP6rOb7GVTMx9uc.png)

- Rss

  ![image-20220725134536786](https://s2.loli.net/2022/08/24/KHmRQ1g3Yk6nIp9.png)

- Disk

  ![image-20220725134809572](https://s2.loli.net/2022/08/24/fQgeyaFsDcujwtZ.png)
  
  ![image-20220725135017922](https://s2.loli.net/2022/08/24/eLEKWbwJatPlCok.png)
