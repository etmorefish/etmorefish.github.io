---
title: ubuntu20 mysql8.0
tags:
  - mysql
categories:
  - Linux
top: false
date: 2020-05-29 22:12:01
---
# 通过apt 安装MySQL服务
> 记一次新机安装笔记
## 1.安装mysql
```bash
#命令1 更新源
sudo apt-get update
#命令2 安装mysql服务
sudo apt-get install mysql-server
```
## 2.初始化配置
```bash
sudo mysql_secure_installation
```

```bash
#1
VALIDATE PASSWORD PLUGIN can be used to test passwords...
Press y|Y for Yes, any other key for No: N (选择N ,不会进行密码的强校验)

#2
Please set the password for root here...
New password: (输入密码)
Re-enter new password: (重复输入)

#3
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them...
Remove anonymous users? (Press y|Y for Yes, any other key for No) : N (选择N，不删除匿名用户)

#4
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network...
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : N (选择N，允许root远程连接)

#5
By default, MySQL comes with a database named 'test' that
anyone can access...
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : N (选择N，不删除test数据库)

#6
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y (选择Y，修改权限立即生效)
```
## 3.检查mysql服务状态
```bash
sudo service mysql status
```
## 4.配置远程访问
```bash
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf #找到 bind-address 修改值为 0.0.0.0(如果需要远程访问)
sudo service mysql restart #重启mysql
```
```bash
sudo mysql -uroot -p
```
```bash
#切换数据库
mysql>use mysql;
#查询用户表命令：
mysql>select User,authentication_string,Host from user;
#查看状态
select host,user,plugin from user;
```
```bash
#设置权限与密码

mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码'; #使用mysql_native_password修改加密规则
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '密码' PASSWORD EXPIRE NEVER; #更新一下用户的密码
mysql> UPDATE user SET host = '%' WHERE user = 'root'; #允许远程访问

#刷新cache中配置 刷新权限
mysql>flush privileges; 
mysql>quit;
```
* 修改密码
```bash
alter user 'root'@'%' identified with mysql_native_password by '密码';
```
* 新增用户赋权并设置远程访问
> mysql8和原来的版本有点不一样，8的安全级别更高，所以在创建远程连接用户的时候，
不能用原来的命令（同时创建用户和赋权）:

```bash
#必须先创建用户（密码规则：mysql8.0以上密码策略限制必须要大小写加数字特殊符号）
mysql> CREATE USER 'sammy'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
#赋权
mysql> GRANT ALL PRIVILEGES ON *.* TO 'sammy'@'%' WITH GRANT OPTION;
```

## 卸载
### 卸载mysql
```bash
dpkg --list|grep mysql        #在终端中查看MySQL的依赖项
sudo apt-get remove mysql-common  #卸载
sudo apt-get autoremove --purge mysql-server-8.0
##sudo apt-get autoremove --purge mysqlxxx
```
### 清理残留数据
```bash
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
1
```
### 再次查看MySQL的剩余依赖项：
```bash
dpkg --list|grep mysql
```
### 继续删除剩余依赖项，如：
```bash
sudo apt-get autoremove --purge mysql-apt-config
```
### 删除原先配置文件
```bash
sudo rm -rf /etc/mysql/ /var/lib/mysql
sudo apt autoremove
sudo apt autoreclean # (如果提示指令有误，就把reclean改成clean)
```

安装MongoDB
```bash
sudo apt install mongodb
```
安装Redis
```bash
sudo apt install redis-server
```


参考：https://blog.csdn.net/weixin_38924500/article/details/106261971