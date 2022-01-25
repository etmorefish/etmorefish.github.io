---
title: Docker安装MySql
tags:
  - Docker
categories:
  - Docker
top: false
date: 2020-05-6 01:48:42
---

#  Docker安装MySql

### 1、查看可用的 MySQL 版本

访问 MySQL 镜像库地址：https://hub.docker.com/_/mysql?tab=tags 。

可以通过 Sort by 查看其他版本的 MySQL，默认是最新版本 **mysql:latest** 。

可以用 **docker search mysql** 命令来查看可用版本

### 2、拉取 MySQL 镜像

```shell
$ docker pull mysql:latest
```

### 3、查看本地镜像

```shell
$ docker images
```

### 4、运行容器

```shell
$ docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

- **-p 3306:3306** ：映射容器服务的 3306 端口到宿主机的 3306 端口，外部主机可以直接通过 **宿主机ip:3306** 访问到 MySQL 的服务。
- **MYSQL_ROOT_PASSWORD=123456**：设置 MySQL 服务 root 用户的密码。

### 5、安装成功

通过 **docker ps** 命令查看是否安装成功：

本机可以通过 root 和密码 123456 访问 MySQL 服务。

```shell
$ mysql -h localhost -u root -p
```

