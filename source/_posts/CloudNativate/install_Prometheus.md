---
title: CentOS7 安装 Prometheus，并配置后台启动
tags:
  - prometheus
categories:
  - tsdb
top: false
date: 2022-07-1 18:33:18
---

# CentOS7 安装 Prometheus，并配置后台启动

> 官网获取下载链接地址：[https://prometheus.io/download/](https://prometheus.io/download/)

##### 1、获取安装包

切换目录

```
[root@Prom ~]# cd /opt/

```

```
[root@Prom opt]#  wget https://github.com/prometheus/prometheus/releases/download/v2.19.0/prometheus-2.19.0.linux-amd64.tar.gz


```

如果下载慢，可以用下载工具，下载后上传至服务器。

##### 2、解压

```
[root@Prom opt]# tar -zxvf prometheus-2.19.0.linux-amd64.tar.gz
[root@Prom opt]# mv prometheus-2.19.0.linux-amd64 prometheus

```

##### 3、运行

切换目录

```
[root@Prom opt]# cd prometheus


```

默认的端口号是 9090，因此要开放 9090 端口号

```
[root@Prom prometheus]# sudo firewall-cmd --add-port=9090/tcp --permanent


```

重新加载防火墙

```
[root@Prom prometheus]# sudo firewall-cmd --reload


```

运行，指定配置文件

```
[root@Prom prometheus]# ./prometheus --config.file=prometheus.yml


```

在网页输入地址：  
**服务器 ip:9090**  
![](https://img-blog.csdnimg.cn/20200418215943525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyNDE1MDYz,size_16,color_FFFFFF,t_70)  
出现以下网页，可以输入以下指令：

```
rate(promhttp_metric_handler_requests_total{code="200"}[1m])


```

![](https://img-blog.csdnimg.cn/20200418220123216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyNDE1MDYz,size_16,color_FFFFFF,t_70)

5、后台启动方式
--------

创建日志文件

```
[root@Prom prometheus]#  mkdir -p /applog/prometheus && touch  /applog/prometheus/prometheus.log


```

方式 一

```
[root@Prom prometheus]# nohup ./prometheus --config.file=prometheus.yml --web.enable-lifecycle > /applog/prometheus/prometheus.log 2>&1 &


```

方式二 （systemctl 启动方式）

启动脚本

```
[root@Prom prometheus]# vi /opt/prometheus/prometheus.sh


```

添加以下内容：

```
#!/bin/bash
/opt/prometheus/prometheus --web.enable-lifecycle --config.file=/opt/prometheus/prometheus.yml &>> /applog/prometheus/prometheus.log


```

授权脚本

```
[root@Prom prometheus]# chmod +x prometheus.sh


```

新增 **prometheus.service**

```
[root@Prom prometheus]# vi /usr/lib/systemd/system/prometheus.service


```

添加以下内容：

```
[Unit]
Description=Prometheus
Documentation=https://prometheus.io/docs/introduction/overview/
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
# 启动脚本
ExecStart=/opt/prometheus/prometheus.sh

[Install]
WantedBy=multi-user.target



```

验证启动

```
[root@Prom prometheus]# systemctl daemon-reload


```

配置开机加载

```
[root@Prom prometheus]# systemctl enable prometheus.service


```

启动 Prometheus

```
[root@Prom prometheus]# systemctl start prometheus.service


```

查看是启动状态

```
[root@Prom prometheus]# systemctl status prometheus


```

6、启动端口号修改
---------

启动参数新增，如果配置了脚本，在脚本中添加–web.listen-address=:8091 （你要修改成的端口号）改参数即可

```
[root@Prom prometheus]# prometheus --config.file=prometheus.yml --web.listen-address=:8091


```

7、重载配置模式
--------

需配置参数 **–web.enable-lifecycle** ，上面后台脚本已经添加

```
[root@Prom prometheus]# curl -X POST http://127.0.0.1:9090/-/reload

```

8、prometheus页面警告
--------
解决方法：在服务器输入命令：ntpdate ntp.aliyun.com 同步时间即可正常~