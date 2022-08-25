---
title: influx_vs_victoriametrics_cluster_test_report 
tags:
  - k8s
  - influxdb
  - victoriametrics
  - benchmark
  - prometheus
categories:
  - tsdb
top: false
date: 2022-07-24 18:33:18
---
# influx_vs_victoriametrics_cluster_test_report

## 环境

- **Prometheus**: 178.104.163.111		 2c4g

- **k8s集群**：178.104.163.88

  > docker version:20.10.16  k8s version: 1.20.6

- **influxdb-cluster**

178.104.163.151 influx-1 		2c4g
178.104.163.26  influx-2 		 4c8g
178.104.163.53  influx-3  		4c8g

> 内核版本：3.10.0-862.el7.x86_64
>
> docker version：20.10.17

**架构图**

![image-20220725111041635](https://s2.loli.net/2022/08/24/7um1iE8blcAx3wa.png)



- **victoriametrics-cluster**

178.104.163.177  tsdb1 	  2c4g
178.104.163.76   tsdb2		4c8g
178.104.163.113  tsdb3		4c8g

**架构图**

![image-20220725112020055](https://s2.loli.net/2022/08/24/xCREeMzPVBLowSH.png)

## 部署方式

- [influxdb_cluster 部署方式](influx_1.6_cluster/README.md)
- [victoriametrics_cluster 部署方式](vmcluster/README.md)

## 测试结果

>  运行时间三天

```sh
# 磁盘本地存储
[root@influx-1 ~]# du -sh /var/lib/influxdb/
1.5G    /var/lib/influxdb/
[root@influx-2 ~]# du -sh /var/lib/influxdb/
707M    /var/lib/influxdb/
[root@influx-3 ~]# du -sh /var/lib/influxdb/
1.7G    /var/lib/influxdb/
--------------------------------
[root@tsdb-cluster-3 ~]# du -sh /var/lib/vm
122M    /var/lib/vm
[root@tsdb-cluster-2 ~]# du -sh /var/lib/vm
174M    /var/lib/vm
[root@tsdb-cluster-1 ~]#  du -sh /var/lib/vm
199M    /var/lib/vm
```

![image-20220725131101361](https://s2.loli.net/2022/08/24/WehX5Ec9sLwRFaT.png)

![image-20220725131036156](https://s2.loli.net/2022/08/24/d9ozSZiHkgY7p3X.png)

![image-20220725130959873](https://s2.loli.net/2022/08/24/t4VsSWCoiYv9FjG.png)

---
![image-20220725121904474](https://s2.loli.net/2022/08/24/26WEjInm7RZACgJ.png )

![image-20220725122029764](https://s2.loli.net/2022/08/24/D7JRMtyXradYlNG.png)

![image-20220725122201004](https://s2.loli.net/2022/08/24/iclMDKn7Z1gR456.png)





- CPU

  ![image-20220725123958988](https://s2.loli.net/2022/08/24/nON9DL3blZJCBvx.png)
  
  ![image-20220725123408776](https://s2.loli.net/2022/08/24/YWiFNJUI2K4cjG8.png)

- Memory

  ![image-20220725124140828](https://s2.loli.net/2022/08/24/D1huadKbHsYOJ6X.png)

  ![image-20220725124516583](https://s2.loli.net/2022/08/24/kHojpOWU9i6cMPR.png)

- Rss

  ![image-20220725124653629](https://s2.loli.net/2022/08/24/6LBkVFfMKrn84G3.png)

  ![image-20220725124743895](https://s2.loli.net/2022/08/24/UW8tzhe2PiwdgBO.png)

- Disk

  ![image-20220725125916072](https://s2.loli.net/2022/08/24/HIWJ4yBUDVCRdn7.png)

  ![image-20220725125151780](https://s2.loli.net/2022/08/24/EL3aCsSfhRyWmvN.png)

  ---

  ![image-20220725130429272](https://s2.loli.net/2022/08/24/TvAQVGtrPN2LFXw.png)

  ![image-20220725153305772](https://s2.loli.net/2022/08/24/1w7fSOWIeLGEMrP.png)
