---
title: victoriametrics_vs_tdengine_single_version
tags:
  - k8s
  - victoriametrics
  - tdengine
  - cadvisor
  - benchmark
  - prometheus
categories:
  - tsdb
top: false
date: 2022-08-8 18:33:18
---

# victoriametrics_vs_tdengine_single_version

## Environment

- **Prometheus**: 178.104.163.111 2c4g

- **Node:** 178.104.163.188  4c8g

  > victoriametrics、tdengine 均安装最新版本作为本次测试
  >
  > docker version：20.10.17 
  >
  > linux kernel version:  3.10.0-957.el7.x86_64 

## Architecture

![image-20220731222438136](https://s2.loli.net/2022/08/24/NMKVR8vDJuz7QPl.png)

## Deploy



## Test Result

> duration: 5 days

```sh
[root@desktop-7f8a ~]# du -sh /var/lib/vm ;du -sh /var/lib/taos
93M     /var/lib/vm
859M    /var/lib/taos

```

```sh
rate(prometheus_tsdb_head_samples_appended_total[1m])
avg(rate(container_cpu_usage_seconds_total{job="vm_td_cadvisor", name=~"vm|tdengine"}[1m])) by (name, image, instance,  job)
container_cpu_usage_seconds_total{job="vm_td_cadvisor", name=~"vm|tdengine"}
container_fs_writes_bytes_total{job="vm_td_cadvisor", name=~"vm|tdengine"}
container_fs_writes_total{job="vm_td_cadvisor", name=~"vm|tdengine"}
```

![image-20220802151311428](https://s2.loli.net/2022/08/24/GEn8bPtMgoUKNce.png)

- CPU

  ![image-20220802151441630](https://s2.loli.net/2022/08/24/B8FmzZjaHnXKuxA.png)

- Memory

  ![image-20220802151534464](https://s2.loli.net/2022/08/24/qMEp6AcTHWFo1z5.png)

- Rss

  ![image-20220802151648122](https://s2.loli.net/2022/08/24/QN2eRbYEsZDpqIu.png)

- IO

  ![image-20220802151728888](https://s2.loli.net/2022/08/24/vBA69xrTlufgazq.png)

  ![image-20220802152224653](https://s2.loli.net/2022/08/24/b2CHjwZKGLWTYIq.png)



> [PromQL] http://178.104.163.111:9090/graph?g0.expr=rate(prometheus_tsdb_head_samples_appended_total%5B1m%5D)&g0.tab=0&g0.stacked=0&g0.show_exemplars=0&g0.range_input=5d&g1.expr=avg(rate(container_cpu_usage_seconds_total%7Bjob%3D%22vm_td_cadvisor%22%2C%20name%3D~%22vm%7Ctdengine%22%7D%5B5m%5D))%20by%20(name%2C%20image%2C%20instance%2C%20%20job)&g1.tab=0&g1.stacked=0&g1.show_exemplars=0&g1.range_input=5d&g2.expr=container_memory_usage_bytes%7Bjob%3D%22vm_td_cadvisor%22%2C%20name%3D~%22vm%7Ctdengine%22%7D&g2.tab=0&g2.stacked=0&g2.show_exemplars=0&g2.range_input=5d&g3.expr=container_memory_rss%7Bjob%3D%22vm_td_cadvisor%22%2C%20name%3D~%22vm%7Ctdengine%22%7D&g3.tab=0&g3.stacked=0&g3.show_exemplars=0&g3.range_input=5d&g4.expr=avg(rate(container_fs_writes_bytes_total%7Bjob%3D%22vm_td_cadvisor%22%2C%20name%3D~%22vm%7Ctdengine%22%7D%5B5m%5D))%20by%20(name%2C%20image%2C%20instance%2C%20%20job)&g4.tab=0&g4.stacked=0&g4.show_exemplars=0&g4.range_input=5d&g5.expr=rate(container_fs_writes_total%7Bjob%3D%22vm_td_cadvisor%22%2C%20name%3D~%22vm%7Ctdengine%22%7D%5B1m%5D)&g5.tab=0&g5.stacked=0&g5.show_exemplars=0&g5.range_input=5d