---
title: Hexo博客也可以有后台了？强大的Hexo后台——Qexo部署记录
date: 2022-07-06 09:01:13
tags: 
 - Vercel
 - Hexo
 - Qexo
 - Serverless
categories: 记录
---

# 前言

Qexo是一个一个快速、强大、漂亮的在线 Hexo 编辑器，它可以部署在Vercel上，使用MongoDB来存储数据

<div class="warning">

> 本文将记录我如何搭建Qexo，并不代表Qexo官方使用文档，官方文档是在[GitHub Wiki](https://github.com/Qexo/Qexo/wiki)
>
> 但这也代表我的记录会更详细，也会更容易帮助你部署Qexo

</div>

项目地址：https://github.com/Qexo/Qexo

# 注册MongoDB

MongoDB是有免费套餐的，所以直接[注册](https://www.mongodb.com/cloud/atlas/register)

问卷直接随便乱填不用管那么多

![](./img/2022-07-06 09.22.57.png)

然后套餐选择免费

![](./img/2022-07-06 09.24.04.png)

![](./img/2022-07-06 09.25.19.png)

点击创建，然后要你设置连接数据库用的用户名和密码

![](./img/2022-07-06 09.27.11.png)

设置好之后到左侧栏的Database，记住你数据库的名字，我的就直接默认Cluster0

![](./img/2022-07-06 09.29.12.png)

点击Connect

![](./img/2022-07-06 09.29.50.png)

![](./img/2022-07-06 09.30.43.png)

![](./img/2022-07-06 09.31.38.png)

![](./img/2022-07-06 09.32.27.png)

![](./img/2022-07-06 09.35.01.png)

# 部署到Vercel

点击按钮开始部署 [![部署到 Vercel](https://camo.githubusercontent.com/5e471e99e8e022cf454693e38ec843036ec6301e27ee1e1fa10325b1cb720584/68747470733a2f2f76657263656c2e636f6d2f627574746f6e)](https://vercel.com/new/clone?repository-url=https://github.com/am-abudu/Qexo)

![](./img/2022-07-06 09.09.58.png)

克隆的名称随便填

第一次部署会出现错误，因为我们还没有设置**环境变量**

![](./img/2022-07-06 09.12.49.png)

到Vercel的项目设置 -> *Environment Variables*

![](./img/2022-07-06 09.16.07.png)

按照下列表格添加环境变量

| 名称         | 意义                                                         | 示例                                    |
| ------------ | ------------------------------------------------------------ | --------------------------------------- |
| DOMAINS      | 你所允许通信的安全域名，也就是访问后台用的域名 注意双引号而且是英文半角 | [".vercel.app", ".yoursite.com"]        |
| MONGODB_HOST | MongoDB 数据库连接地址                                       | mongodb+srv://cluster0.xxxx.mongodb.net |
| MONGODB_PORT | MongoDB 数据库通信端口 默认应填写 27017                      | 27017                                   |
| MONGODB_USER | MongoDB 数据库用户名                                         | abudu                                   |
| MONGODB_DB   | MongoDB 数据库名                                             | Cluster0                                |
| MONGODB_PASS | MongoDB 数据库密码                                           | JWo0xxxxxxxx                            |

设置完成后，点击上面的*Deployments*

![](./img/2022-07-06 09.40.51.png)

选择里面部署错误的那一项，选*Redeploy*

![](./img/2022-07-06 09.41.30.png)

![](./img/2022-07-06 09.41.36.png)

过一会就能顺利看见后台初始化向导了

![](./img/2022-07-06 09.44.11.png)

# 初始化

用户密码自定义Qexo API密钥这些的就不多讲了，只讲重要的部分

## Github仓库

这里要设置你的Hexo的Github Actions自动部署仓库或Vercel自动部署仓库，或者说是你的Hexo源文件仓库

![](./img/2022-07-06 09.46.13.png)

仓库名称、分支、路径这些的你应该知道怎么填的

密钥要到你的[Github 设置](https://github.com/settings/tokens)去生成一个

![](./img/2022-07-06 09.48.46.png)

![](./img/2022-07-06 09.49.19.png)

## 图床设置

如果你没有COS这些的像我一样直接放在Vercel可以直接跳过，只是不能在Qexo后台上传照片了

## Vercel配置

密钥要到[设置](https://vercel.com/account/tokens)生成一个

![](./img/2022-07-06 09.52.43.png)

然后填Project ID，前往你的Qexo项目设置

![](./img/2022-07-06 09.54.07.png)

鼠标滑到最下面就可以看到了

![](./img/2022-07-06 09.54.13.png)

# 结尾

至此，恭喜你有了一个基于Qexo的Hexo后台，它可以帮助你更方便的进行写作

![](./img/2022-07-06 09.55.45.png)

同时，在这里说一下，如果Qexo有缓存不刷新（比如文章列表不出来新文章之类的）就到左上角的齿轮进入设置

![](./img/2022-07-06 09.57.07.png)

那么本文到此结束，再见
