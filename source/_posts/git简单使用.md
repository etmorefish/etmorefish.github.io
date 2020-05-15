---
title: 标签测试
date: 2020-03-06 14:03:04
tags: 
	- Git
	- Linux
---
#### git简单的使用方法:

`1、  cd ~/.ssh/`    如果没有对应的文件夹，则执行  `mkdir  ./.ssh`

`2、git config --global user.name "lilei"`

`3、git config --global user.email "849078367@qq.com"`

`4、ssh-keygen -t rsa -C "849078367@qq.com"`

执行上面命令后在home目录生成了`id_rsa.pub`秘钥文件

 `ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC9zTKWSeYAGIREZDUrvEX9JCoK1Th2ME7Mu/RbRg6o+MJqlrhi5w1DTl+VOkbwKJEGPwdMV3pl3cCF74ksFT0MrvHxOlqV1TQAHVN85Lp/JALPPO27eJsKtgkeHN0nreJEyEteJdqrDmeyhOdDKp1iC0Ec+9H3apaSRPCiB5hghMCucxoRabRgqOt04Yn0XxYsYXw6Oe3cWAMvkrlSwCk1SXQLqoyCdEX7KW0+gFnYivbo4Rnmjh7FiVrtqtlgJJ3siE4EnA7H9fpzpDo1cp0Yd0rQIbLjuib0TNCUHkToWe9EaApfCVfdTkhr42vwwJPyPk0qz4DxaiwXEcvoVTnv 849078367@qq.com`
 把秘钥复制到gitlib>>setings>>SSH Keys`http://124.204.65.84:12463/profile/keys`

![](/home/lei/TJ/note/2019-07-11 15-41-30屏幕截图.png)



创建项目文件目录，然后执行`git init`   ````````````````````````

添加远程仓库：`git remote add origin http://124.204.65.84:12463/spider/datacrawl.git`

克隆远程仓库：`git clone http://gitlab.example.com/spider/datacrawl.git`

拉取远程仓库代码：`git pull origin feature/additional`

`feature/additional`这个是我们所在的分支

推送本地代码到远程仓库：`git push origin feature/additional`或`git push`（ps:之后每次上传代码可以用这命令）

配置文件在当前项目目录 为隐藏文件夹 .`git`下面的config

修改`config文件`示例：

``

```linux
  1 [core]
  2         repositoryformatversion = 0
  3         filemode = true
  4         bare = false
  5         logallrefupdates = true
  6 [remote "origin"]
  7         url = http://124.204.65.84:12463/spider/datacrawl.git
  8         fetch = +refs/heads/*:refs/remotes/origin/*
  9 [branch "feature/additional"]
 10         remote = origin
 11         merge = refs/heads/feature/additional
 12 [branch "develop"]
 13         remote = origin
 14         merge =  refs/heads/develop
 15 [user]
 16         name = maolei
 17         email = 849078367@qq.com
 18 [credential]
 19         helper = store
~                                 
```

第一次会输入用户名和密码，之后就不会了，

#### 推送本地代码到远程仓库：

`git add .`提交所有文件到暂存区

`git commot -m "对本次提交的描述"`

`git status` 查看当前状态

`git push`把刚刚提交到暂存区的文件推送到远程仓库

每次推送之前一个`git pull`一下，避免和远程更新发生冲突，会提示 `git pull`

**其他的一些用法可以参考廖雪峰的git教程**https://www.liaoxuefeng.com/wiki/896043488029600



保留当前活动  `git stash`
`切换分支`  `git checkout feature/additional` 