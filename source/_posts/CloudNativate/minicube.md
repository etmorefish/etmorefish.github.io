---
title: Minicube 
tags:
  - k8s
  - minikube
categories:
  - k8s
top: false
date: 2022-06-24 18:33:18
---

# Minicube

## minikebu start

### 1. Install

要在 x86-64 Linux 上使用二进制下载安装最新的 minikube 稳定版本:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
> 添加到shell配置文件 `alias kubectl="minikube kubectl --"`
### 2. Start your cluster

`minikube start`

### 3. Interact with your cluster

```bash
kubectl get po -A
```
####  Kubernetes Dashboard
`minikube dashboard`


## 4. Deploy applications

创建一个示例部署并在端口8080上公开它:

`kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4`

`kubectl expose deployment hello-minikube --type=NodePort --port=8080`

这可能需要一些时间，但是当您运行时，您的部署很快就会显示出来:

`kubectl get services hello-minikube`

访问这项服务最简单的方法是让 minikube 为你启动一个网页浏览器:

`minikube service hello-minikube`

或者，使用 kubectl 转发端口:

`kubectl port-forward service/hello-minikube 7080:8080`

你的应用程序现在可以在 google http://localhost:7080/上使用了。
> Note：
 k8s.gcr.io 换成 registry.cn-hangzhou.aliyuncs.com/google_containers

### LoadBalancer deployments

要访问 LoadBalancer 部署，可以使用“ minikube tunnel”命令:

`kubectl create deployment balanced --image=k8s.gcr.io/echoserver:1.4`  
`kubectl expose deployment balanced --type=LoadBalancer --port=8080`

在另一个窗口中，启动隧道为平衡部署创建一个可路由的 IP:

`minikube tunnel`

要查找可路由的 IP，请运行以下命令并检查 external-IP 列:

`kubectl get services balanced`

您的部署现在可以在 < external-ip > : 8080获得


## 5. Manage your cluster 

暂停 Kubernetes 而不影响已部署的应用程序:

```shell
minikube pause
```

打开暂停的实例:

```shell
minikube unpause
```

停止群集:

```shell
minikube stop
```

提高默认内存限制(需要重新启动) :

```shell
minikube config set memory 16384
```

浏览易于安装的 Kubernetes 服务的目录:

```shell
minikube addons list
```

创建第二个集群，运行老版本的 Kubernetes:

```shell
minikube start -p aged --kubernetes-version=v1.16.1
```

删除所有的 minikube 集群:

```shell
minikube delete --all
```
