---
title: django小结
tags:
  - Python
  - Django
categories:
  - Python
top: false
date: 2020-07-01 14:34:04
---
# django小结

![django小结](https://i.loli.net/2020/07/07/eyYNL7mpBqOAPtZ.png)

## 请求的生命周期

### wsgi -> 请求中间件 -> 路由分配 -> 视图 -> 响应中间件 -> wsgi

```
	1.请求发送到 wsgi ，wsgi 封装请求的相关数据（request）
	2.django 去匹配路径，根据路处理具体的业务逻辑
	3.执行函数， 函数中处理具体的业务逻辑
	4.函数返回响应， Django按照HTTP 协议的响应的格式进行返回
```



## 中间件

### 简单来说中间键是帮助我们是视图函数执行前和执行后都做一些额外的操作，包装如装饰器

## 使用 orm 和原生 sql 的缺点

### orm 优点

- 快速开发

### orm 缺点

- 复杂查询可能会有性能问题

### 原生 sql 优点

- 运行复杂查询更加灵活

### 原生 sql 缺点

- 编写需小心，防止 sql 注入

## 概念

### 什么是 wsgi

- web server 和 web application 通信的一种规范

### 什么是 FBV 和 CBV

- 处理方式

	- 类处理方式（class base views）
	- 函数处理方式（function base views）

## Django、Flask、Tornado 对比

### Django 走的是大而全的方向，开发效率高。采用 MTV 设计模式，自带 ORM 组件，提高了开发效率

### Flask 是轻量级的框架，自由、灵活、可扩展性强，核心基于 Werkzeug 和 Jinja2

### Tornada 走的是少而精的方向，性能优越，最出名是异步非阻塞能力

## 安全

### CSRF (跨站请求伪造)

- 是一种挟制用户在当前已登录的 web 应用程序上执行非本意的操作攻击方式
- 令牌同步模式
- 检查 Referer 字段
- 添加 token

### XSS

- 利用网页开发时留下的漏洞，通过巧妙的方法注入恶意指令到网页，使用用户加载并执行攻击者的恶意代码

## django 中 csrf的实现机制

### 令牌同步模式

## session 和 cookie的区别

### session 是以对象的形式保存在服务器中

### cookie 是以字符串的形式保存在客户端

## restful规范

*XMind - Trial Version*