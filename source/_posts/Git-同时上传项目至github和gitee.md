---
title: Git 同时上传项目至github和gitee
tags:
  - Git
categories:
  - Git
top: false
date: 2020-04-27 22:36:19
---

# Git 同时上传项目至github和gitee

## 1. 删除本地项目git默认远程库名称

```shell
//git默认远程库名称为origin
 git remote rm origin
```

## 2. 自定义关联github和gitee并设置名称

```shell
//关联github并设置别名为github
$ git remote add github git@github.com:lei025/tcb-hackthon-secondHand.git

//关联gitee并设置别名为gitee
$ git remote add gitee git@gitee.com:leidb/tcb-hackthon-secondHand.git

```

## 3. 推送到远程仓库

* 推送到github

  `git push github master`

* 推送到gitee

  `git push gitee master`



由此，本地库就可以同时与多个远程库相互同步代码了。



[Git使用方法（精心整理，绝对够用）](https://blog.csdn.net/xukai0110/article/details/80637902)