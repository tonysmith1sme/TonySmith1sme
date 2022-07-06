---
title: 关于我第一次使用Next.js这件事
date: 2022-06-26 22:32:31
tags: 
 - Next.js
 - Serverless
categories: 记录
---

# Next.js是什么

<div class="info">

> Next.js 是一个构建于Node.js 之上的开源Web 开发框架，支持基于React 的 Web 应用程序功能，例如服务端渲染和生成静态网站。 React 于官方文件内的“推荐的工具链”中提及Next.js，建议将其作为“使用 Node.js 构建服务器渲染网站”的解决方案。
>
> *——[Wikipedia](https://zh.wikipedia.org/zh-cn/Next.js)*

</div>

# 关于我用Next.js这件事

最近乱搞了一个东西需要用到一个静态页面，因为我懒得造轮子又突然想起来有个叫Next.js的东西于是乎我开始了噩梦的自学过程

## 为什么我使用Next.js

Next.js非常先进，想要快速生成一个整体的静态网站用它是很不错的选择

同时，Next.js还支持Markdown，这个是非常好的

## 从Vercel上偷模板

首先如果你不知道Vercel是什么，那我先跟你介绍一下

Vercel是一个Serverless的搭建平台，他跟Github Pages的有两个不同

1.它可以直接导入源部署

2.Vercel AGA网络在中国大陆地区的访问速度会更快

那么Vercel对新手也是很照顾的，准备了一堆模板



我使用模板的原因是因为我是初次接触Next.js，学习一个新潮的东西最好的开始就是改模板

![](./img/2022-06-26_22-38.png)

其中就有Next.js

那么我选择了[Blog Starter Kit](https://vercel.com/new/clone?demo-description=Markdown%20powered%20portfolio.%20Built%20with%20Next.js.&demo-image=https%3A%2F%2Fassets.vercel.com%2Fimage%2Fupload%2Fv1618005400%2Ffront%2Fhome%2Fnew%2Fnextra-blog.png&demo-title=Blog%20Starter%20Kit&demo-url=https%3A%2F%2Fdemo.vercel.blog%2F&project-name=portfolio&repository-name=portfolio&s=https%3A%2F%2Fgithub.com%2Fvercel%2Fnextjs-portfolio-starter&from=templates) ([GIthub](https://github.com/vercel/nextjs-portfolio-starter))，我选这个模板的原因是因为这个有一个完整的博客框架，比较适合拿来演示

![](./img/2022-06-26_22-42.png)

选择好模板直接 *Create* 就可以了

## 修改页面

Next.js存放页面的文件夹是 `/pages/`

![](./img/2022-06-26_22-46.png)

可以看到有现成的 `mdx` 文件（实际上就是另类的爆改版Markdown）

![](./img/2022-06-26_22-47.png)

这个模板的Next.js还是很好改的，只要你会用Markdown

<div class="info">

> 当然我也用过不好改的tsx形式的Next.js模板，目前已经在Vercel模板中移除了，但是在Github上还有仓库([vercel/preview-mode-demo](https://github.com/vercel/preview-mode-demo))

</div>

直接在Github修改并 *commit* ，Vercel在收到新的提交之后就会自动开始部署了

# 例子：插入图片

在Next.js你不能使用常规的方法去添加图片，接下来我会简单讲一下如何插入图片

首先你要在页面文件中加入 *next/image* 模块

将下列代码加入到页面文件的某一行（比如首行或尾行）

```jsx
import Image from 'next/image'
```

然后就可以添加图片了，以下是添加一张图片的例子

```jsx
<Image
  src="/images/photo2.jpg" <!--图片链接-->
  alt="Photo" <!--I think you know what is "alt"-->
  width={1125} <!--I think you know what is "width"-->
  height={750} <!--I think you know what is "height"-->
  priority <!--I think yo uknow what is "priority"-->
/>
```

参考Next.js的 [next/image](https://nextjs.org/docs/api-reference/next/image) 文档

# 尾巴

最后，Next.js是好东西，但是因为某些原因你可能需要去适应一些不同（？
