---
title: docker笔记
tags:
  - Docker
categories:
  - Docker
top: false
date: 2020-07-15 21:33:18
---

<table bgcolor=#343a40 >
    <tr>
        <td><img src='https://www.docker.com/sites/default/files/d8/styles/medium/public/2020-04/dockercon2020_logobl.png?itok=Z6k3qZKP' ></td>
  </tr>
    </table>
## 镜像的操作

### 1.拉取镜像

```bash
从中央仓库拉取镜像到本地
docker pull 镜像名称[:tag]
#举个栗子:docker pull daocloud.io/library/tomcat:8.5.15-jre8
```

### 2.查看本地全部镜像

```bash
查看本地已经安装过的镜像信息，包含标识，名称，版本，更新时间，大小
docker images
```

### 3.删除本地镜像

```bash
镜像会占用磁盘空间，可以直接手动删除，标识通过查看获取
docker rmi #镜像的标识
```

### 4.镜像的导入导出

```bash
如果因为网络原因可以通过硬盘的方式传输镜像，虽然不规范，但是有效，但是这种方式导出的镜像名称和版本都是null，需要手动修改
#将本地的镜像导出
docker save -o 导出的路径 镜像id
#加载本地的镜像文件
docker load -i 镜像文件
#修改镜像文件docker tag 镜像id 新镜像名称：版本
```

## 容器的操作

### 1.运行容器

```bash
#运行容器需要定制具体镜像，如果镜像不存在，会直接下载
#简单操作
docker run 镜像的标识|镜像的名称[:tag]
#常用的参数
docker run -d -p 宿主机端口:容器端口 --name 容器名称 镜像的标识|镜像名称[:tag]
#-d:代表后台运行容器
#-p 宿主机端口:容器端口：为了映射当前Linux的端口和容器的端口
#--name 容器名称:指定容器的名称

### 例：运行MySQL容器

docker run -d -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=root daocloud.io/library/mysql:5.7.4

```

### 2.查看正在运行的容器

```bash
查看全部正在运行的容器信息
docker ps [-qa]
#-a 查看全部的容器，包括没有运行
#-q 只查看容器的标识
```

### 3.查看容器日志

```bash
查看容器日志，以查看容器运行的信息
docker logs -f 容器id
#-f：可以滚动查看日志的最后几行
```

### 4.进入容器的内部

```bash
可以进入容器的内部进行操作
docker exec -it 容器id bash
```

### 5.复制内容到容器

```bash
将宿主机的文件复制到容器内部的指定目录
docker cp 文件名称 容器id:容器内部路径
```

### 6.重启&启动&停止&删除容器

```bash
容器的启动，停止，删除等操作
#重新启动容器
docker restart 容器id
#启动停止运行的容器
docker start 容器id 
#停止指定的容器(删除容器前，需要先停止容器)
docker stop 容器id
#停止全部容器
docker stop $(docker ps -qa)
#删除指定容器
docker rm 容器id
#删除全部容器
docker rm $(docker ps -qa)
```

## 数据卷

数据卷：将宿主机的一个目录映射到容器的一个目录中。

可以在宿主机中操作目录中的内容，那么容器内部映射的文件，也会跟着一起改变。

### 1.创建数据卷

```bash
#创建数据卷后，默认会存放在一个目录下/var/lib/docker/volumes/数据卷名称/_data
docker volume create 数据卷名称
```

### 2.查看全部数据卷

```bash
#查看全部数据卷信息
docker volume ls
```

### 3.查看数据卷详情

```bash
#查看数据卷的详细信息，可以查询到存放的路径，创建时间等等
docker volume inspect 数据卷名称
```

### 4.删除数据卷

```bash
#删除指定的数据卷
docker volume rm 数据卷名称
```

### 5.容器映射数据卷

```bash
#通过数据卷名称映射，如果数据卷不存在。Docker会帮你自动创建，会将容器内部自带的文件，存储在默认的存放路径中。
docker run -d -p 8080:8080 --name mysql -v 数据卷名称:容器内部的路径 镜像id 
#通过路径映射数据卷，直接指定一个路径作为数据卷的存放位置。但是这个路径下是空的。
docker run -d -p 8080:8080 --name mysql -v 路径(/root/自己创建的文件夹):容器内部的路径 镜像id
```



## Docker Dockerfile

### 什么是 Dockerfile？

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

### 使用 Dockerfile 定制镜像

```dockerfile
创建自定义镜像就需要创建一个Dockerfiler,如下为Dockerfile的语言
 
from：指定当前自定义镜像依赖的环境
copy：将相对路径下的内容复制到自定义镜像中
workdir：声明镜像的默认工作目录
run：执行的命令，可以编写多个
cmd：需要执行的命令（在workdir下执行的，cmd可以写多个，只以最后一个为准）
 
```

**1、下面以定制一个 nginx 镜像（构建好的镜像内会有一个 /usr/share/nginx/html/index.html 文件）**

在一个空目录下，新建一个名为 Dockerfile 文件，并在文件内添加以下内容：

```shell
FROM nginx
RUN echo '这是一个本地构建的nginx镜像' > /usr/share/nginx/html/index.html
```

**2、FROM 和 RUN 指令的作用**

**FROM**：定制的镜像都是基于 FROM 的镜像，这里的 nginx 就是定制需要的基础镜像。后续的操作都是基于 nginx。

**RUN**：用于执行后面跟着的命令行命令。有以下俩种格式：

shell 格式：

```
RUN <命令行命令>
# <命令行命令> 等同于，在终端操作的 shell 命令。
```

exec 格式：

```shell
RUN ["可执行文件", "参数1", "参数2"]
# 例如：
# RUN ["./test.php", "dev", "offline"] 等价于 RUN ./test.php dev offline
```

**注意**：Dockerfile 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层，会造成镜像膨胀过大

### 开始构建镜像

在 Dockerfile 文件的存放目录下，执行构建动作。

以下示例，通过目录下的 Dockerfile 构建一个 nginx:test（镜像名称:镜像标签）。

**注**：最后的 **.** 代表本次执行的上下文路径，下一节会介绍。

```shell
#编写完Dockerfile后需要通过命令将其制作为镜像，并且要在Dockerfile的当前目录下，之后即可在镜像中查看到指定的镜像信息，注意最后的 .
docker build -t 镜像名称[:tag] ./

$ docker build -t nginx:test .
```

上下文路径，是指 docker 在构建镜像，有时候想要使用到本机的文件（比如复制），docker build 命令得知这个路径后，会将路径下的所有内容打包。

**解析**：由于 docker 的运行模式是 C/S。我们本机是 C，docker 引擎是 S。实际的构建过程是在 docker 引擎下完成的，所以这个时候无法用到我们本机的文件。这就需要把我们本机的指定目录下的文件一起打包提供给 docker 引擎使用。

如果未说明最后一个参数，那么默认上下文路径就是 Dockerfile 所在的位置。

>  **注意**：上下文路径下不要放无用的文件，因为会一起打包发送给 docker 引擎，如果文件过多会造成过程缓慢。

# Docker Compose

### Compose 简介

Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

如果你还不了解 YML 文件配置，可以先阅读 [YAML 入门教程](https://www.runoob.com/w3cnote/yaml-intro.html)。

Compose 使用的三个步骤：

- 使用 Dockerfile 定义应用程序的环境。
- 使用 docker-compose.yml 定义构成应用程序的服务，这样它们可以在隔离环境中一起运行。
- 最后，执行 docker-compose up 命令来启动并运行整个应用程序。

### 1.下载并安装Docker-Compose

### 1.1下载Docker-Compose

```bash
#去github官网搜索docker-compose，下载1.24.1版本的Docker-Compose下载路径：https://github.com/docker/compose/releases/download/1.24.1/docker-compose-Linux-x86_64
```

### 1.2设置权限

```bash
#需要将DockerCompose文件的名称修改一下，给予DockerCompose文件一个可执行的权限
mv docker-compose-Linux-x86_64 docker-compose
chmod 777 docker-compose
```

### 1.3配置环境变量

```bash
#方便后期操作，配置一个环境变量
#将docker-compose文件移动到了/usr/local/bin，修改了/etc/profile文件，给/usr/local/bin配置到了PATH中 
mv docker-compose /usr/local/bin
vi /etc/profile
#添加内容：export PATH=$JAVA_HOME:/usr/local/bin:$PATH
source /etc/profile
```

### 1.4测试

```bash
在任意目录下输入
docker-compose
```

### 2.Docker-Compose管理MySQL和Tomcat容器

```yaml
# yml文件以key:value方式来指定配置信息
# 多个配置信息以换行+缩进的方式来区分
# 在docker-compose.yml文件中，不要使用制表符 

version: '3.1'
services:  
  mysql:           # 服务的名称    
    restart: always   # 代表只要docker启动，那么这个容器就跟着一起启动    
    image: daocloud.io/library/mysql:5.7.4  # 指定镜像路径    
    container_name: mysql  # 指定容器名称    
    ports:      
      - 3306:3306   #  指定端口号的映射    
    environment:      
      MYSQL_ROOT_PASSWORD: root   # 指定MySQL的ROOT用户登录密码      
      TZ: Asia/Shanghai        # 指定时区    
      volumes:     
        - /opt/docker_mysql_tomcat/mysql_data:/var/lib/mysql   # 映射数据卷 
```

### 3.使用docker-compose命令管理容器

```bash
在使用docker-compose的命令时，默认会在当前目录下找docker-compose.yml文件 
#1.基于docker-compose.yml启动管理的容器
docker-compose up -d 
#2.关闭并删除容器
docker-compose down 
#3.开启|关闭|重启已经存在的由docker-compose维护的容器
docker-compose start|stop|restart 
#4.查看由docker-compose管理的容器
docker-compose ps 
#5.查看日志
docker-compose logs -f
```

### 4.docker-compose配合Dockerfile使用


使用docker-compose.yml文件以及Dockerfile文件在生成自定义镜像的同时启动当前镜像，并且由docker-compose去管理容器

## CI、CD介绍及准备![Screenshot_20200807_004021_tv.danmaku.bili](https://i.loli.net/2020/08/08/apNKYu5iIt31cPS.jpg)



### CI（continuous intergration）持续集成

持续集成：编写代码时，完成了一个功能后，立即提交代码到Git仓库中，将项目重新的构建并且测试。

1.快速发现错误。
2.防止代码偏离主分支。

实现CI，需要使用到Gitlab远程仓库，先通过Docker搭建Gitlab
创建一个全新的虚拟机，并且至少指定4G的运行内存，4G运行内存是Gitlab推荐的内存大小。
并且安装Docker以及Docker-Compose

### CD 持续交付/部署（Continuous Delivery / Deployment）

完成 CI 中构建及单元测试和集成测试的自动化流程后，持续交付可自动将已验证的代码发布到存储库。为了实现高效的持续交付流程，务必要确保 CI 已内置于开发管道。持续交付的目标是拥有一个可随时部署到生产环境的代码库。

在持续交付中，每个阶段（从代码更改的合并，到生产就绪型构建版本的交付）都涉及测试自动化和代码发布自动化。在流程结束时，运维团队可以快速、轻松地将应用部署到生产环境中。

对于一个成熟的 CI/CD 管道来说，最后的阶段是持续部署。作为持续交付——自动将生产就绪型构建版本发布到代码存储库——的延伸，持续部署可以自动将应用发布到生产环境。由于在生产之前的管道阶段没有手动门控，因此持续部署在很大程度上都得依赖精心设计的测试自动化。

# Docker Machine

### 简介

Docker Machine 是一种可以让您在虚拟主机上安装 Docker 的工具，并可以使用 docker-machine 命令来管理主机。

Docker Machine 也可以集中管理所有的 docker 主机，比如快速的给 100 台服务器安装上 docker。

Docker Machine 管理的虚拟主机可以是机上的，也可以是云供应商，如阿里云，腾讯云，AWS，或 DigitalOcean。

使用 docker-machine 命令，您可以启动，检查，停止和重新启动托管主机，也可以升级 Docker 客户端和守护程序，以及配置 Docker 客户端与您的主机进行通信。