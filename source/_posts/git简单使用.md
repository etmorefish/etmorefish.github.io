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

# git本地版本回退与远端版本回退(回滚)

> 一个commit对应这一个版本，有一个commit id，40位的16进制数字，通过SHA1计算得到，不同的文件计算出来的SHA1值不同(有很小的几率相同，可忽略)，这样每一个提交都有其独特的id。每提交一个新版本，实际上Git就会把它们自动串成一条时间线。
> 在Git中，HEAD表示当前版本，也就是e620a6ff0940a8dff…，HEAD^表示上一个版本，HEAD^^表示上上一个版本，往上100个版本可以写成HEAD加连续100个^，也可以写成：HEAD~100。
>

**git log：**该命令显示从最近到最远的提交日志。

**git log --pretty=oneline：**将只会显示提交的commit id号和对应的注释。(这里是两个-，Markdown显示两个-为一个-)

**git reset –hard commit_id 或则是 git reset –hard HEAD^**

```
git reset --hard HEAD^   # hard选项，表示彻底将工作区、暂存区和版本库记录恢复到指定的版本库
```

使用git reset –hard 进行版本回退之后，在本地查看README.md，里面已经变为版本2对应的内容了。

##　**如果你在本地做了错误提交，那么回退版本的方法很简单**
1.先用下面命令找到要回退的版本的commit id：

```
git reflog 1
```

2.接着回退版本:

```
git reset --hard a7e1d279
```

a7e1d279就是你要回退的版本的commit id的前面几位。

## 远程分支版本回退的方法
如果你的错误提交已经推送到自己的远程分支了，那么就需要回滚远程分支了。
1.首先要回退本地分支：

```shell
git reflog
git reset --hard Obfafd
```

2.紧接着强制推送到远程分支：

```shell
git push -f origin master ## 这里假设只有一个master分支
```

origin就是一个名字，它是在你clone一个托管在Github上代码库时，git为你默认创建的指向这个远程代码库的标签，origin指向的是repository，master只是这个repository中默认创建的第一个branch。当你git push的时候因为origin和master都是默认创建的，所以可以这样省略。


> 注意：本地分支回滚后，版本将落后远程分支，必须使用强制推送覆盖远程分支，否则无法推送到远程分支
>  

