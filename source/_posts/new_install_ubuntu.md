---
title: 新安装 Ubuntu 必备工具
tags:
  - Ununtu
categories:
  - Linux
top: false
date: 2022-01-1 22:12:01
---
# 新安装 Ubuntu 必备工具

1.更换软件源

2.更换中文目录名称

```bash
export LANG=en_US
xdg-user-dirs-gtk-update
export LANG=zh_CN

```

3.安装常用工具

```bash
# ssh net-tools vim htop git
sudo apt install *

```

4.安装docker

```bash
# 1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:
 sudo apt-get update
 sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# 2. Add Docker’s official GPG key:
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# 3. Use the following command to set up the stable repository. 
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
# 1. update index and install and install the latest version
 sudo apt-get update
 sudo apt-get install docker-ce docker-ce-cli containerd.io

# 2. Verify that Docker Engine is installed correctly by running the hello-world image.
sudo docker run hello-world

# docker permission denied 
# 1. 新建docker分组
sudo groupadd docker

# 2. 将当前用户添加进分组里面
sudo usermod -aG docker ${USER}

# 3. 然后重新登录，以便让权限生效


# docker 更换淘宝源
sudo vim /etc/docker/daemon.json

{
  "registry-mirrors": ["https://klq9hjzs.mirror.aliyuncs.com"]
}


# Install docker-compose
# 1. Run this command to download the current stable release of Docker Compose:
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 2. Apply executable permissions to the binary:
sudo chmod +x /usr/local/bin/docker-compose

# Test the installation.
docker-compose --version
```

5.生产软件

```bash
# Pycharm
sudo snap install [pycharm-professional|pycharm-community] --classic

# Postman
sudo snap install postman

# Redis Desktop Manager
sudo snap install redis-desktop-manager

# Typora
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -

# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update

# install typora
sudo apt-get install typora
```

6.优化

```bash
# 1. 安装 tweek
sudo apt install gnome-tweak-tool

# 2. 安装插件扩展支持
# 让 gnome 支持插件扩展
sudo apt install gnome-shell-extensions 

# chrome 浏览器扩展支持，可以使用浏览器安装插件
sudo apt install chrome-gnome-shell
```

7.解决apt-get安装中的E: Sub-process /usr/bin/dpkg returned an error code (1)问题

```bash
cd /var/lib/dpkg/
sudo mv info/ info_bak          # 现将info文件夹更名
sudo mkdir info                 # 再新建一个新的info文件夹
sudo apt-get update             # 更新
sudo apt-get -f install         # 修复
sudo mv info/* info_bak/        # 执行完上一步操作后会在新的info文件夹下生成一些文件，现将这些文件全部移到info_bak文件夹下
sudo rm -rf info                # 把自己新建的info文件夹删掉
sudo mv info_bak info           # 把以前的info文件夹重新改回名

```

8.oss-browser

```bash
# desktop icon

sudo vim /usr/share/applications/oss.desktop

[Desktop Entry]
Name=oss
Comment=a ali tool for pic and video
Exec=oss
Icon=/opt/oss-browser-linux-x64/resources/custom/icon.png
Terminal=false
Type=Application
Categories=Development

```

9.数据库

```bash

# MySQL
docker run -itd --name mysql --restart=always -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql

# redis
docker run -itd --name redis --restart=always -p 6379:6379  redis

# postgres
docker run --name some-postgres --restart=always -e POSTGRES_PASSWORD=123456 -p 5432:5432 -d postgres

# mongodb
docker run -itd --name mongo -p 27017:27017 --restart=always mongo --auth

docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
>  db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
# 尝试使用上面创建的用户信息进行连接。
> db.auth('admin', '123456')
```

10.Celery

```bash

```

10.RabbitMQ

If you’re using Ubuntu or Debian install RabbitMQ by executing this command:

$ sudo apt-get install rabbitmq-server
Or, if you want to run it on Docker execute this:

$ docker run -d -p 5672:5672 rabbitmq
When the command completes, the broker will already be running in the background, ready to move messages for you: Starting rabbitmq-server: SUCCESS.

```bash

docker run -it  --name rabbitmq -p 5672:5672 -p 15672:15672 -d rabbitmq:3.9-management

# 启动 rabbitmq 服务
service rabbitmq-server start

# 关闭 rabbitmq 服务
service rabbitmq-server stop

# 重启 rabbitmq 服务
service rabbitmq-server restart

# 查看 rabbitmq 状态
service rabbitmq-server status
# 添加用户
sudo rabbitmqctl add_user  admin  admin  

# 赋予权限
sudo rabbitmqctl set_user_tags admin administrator

# 赋予 virtual host 中所有资源的配置、写、读权限
sudo rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'

# rabbitmq_manager

# rabbitmq_manager 是官方提供的管理工具，官方地址：https://www.rabbitmq.com/management.html

# 启用 rabbitmq_manager
cd /etc/rabbitmq
sudo rabbitmq-plugins enable rabbitmq_management
 
# 访问 rabbitmq_manager：
# 地址：http://localhost:15672
# 账户：admin
# 密码：admin
```
