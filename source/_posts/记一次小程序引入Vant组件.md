---
title: 记一次小程序引入Vant组件
tags:
  - 微信小程序
  - 云开发
categories:
  - B站
top: false
date: 2020-06-06 16:11:05
---
# Vant组件
> https://youzan.github.io/vant-weapp/#/intro
## 引入vant
1. 通过 npm 安装
> 需要注意的是 package.json 和 node_modules 必须在 miniprogram 目录下， 若miniprogram里没有package.json,需执行`npm init`

`npm i @vant/weapp -S --production`
2. 构建 npm 包

    打开微信开发者工具，点击 工具 -> 构建 npm，并勾选 使用 npm 模块 选项，构建完成后，即可引入组件
3. 修改 app.json
 将 app.json 中的 "style": "v2" 去除，小程序的新版基础组件强行加上了许多样式，难以去除，不关闭将造成部分组件样式混乱。

 # 正确引入toast
json页面
`"usingComponents": {
  "van-toast": "@vant/weapp/toast/index"
}`

js页面
 `import Toast from '../../miniprogram_npm/@vant/weapp/toast/toast'
`

wxml页面
`<van-toast id="custom-selector" />`