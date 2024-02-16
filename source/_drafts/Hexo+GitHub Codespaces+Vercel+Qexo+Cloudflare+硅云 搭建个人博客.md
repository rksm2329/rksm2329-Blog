---
abbrlink: Hexo+GitHub Codespaces+Vercel+Qexo+Cloudflare+硅云 搭建个人博客
categories: []
date: '2024-02-16T12:27:07.919033+08:00'
tags: []
title: Hexo+GitHub Codespaces+Vercel+Qexo+Cloudflare+硅云 搭建个人博客
updated: '2024-02-16T12:27:09.185+08:00'
---
# stO %%% lfyszy %%% Orz!!!

# 前言

为什么要大费周章弄一个博客？直接用 `Luogu, CSDN, cnBlogs` 等现成的不行吗？

~~答案是：显得自己有实力~~（（（

`Luogu` 博客前途还是未知，`cnBlogs` 为了活着在个人博客中加入了大量的广告，`CSDN` 相信就不用说了，最离谱的一个。

---

正文开始

# 准备

首先，你需要一台电脑，还有一个邮箱、一个 `GitHub` 账户（关于 `GitHub` 后面会说 QwQ）、一个加速器，如果不需要完全外网 `Watt Toolkit` 可以满足你的需求，如果需要完整外网环境，那么 `Geph` 就行了。

## GitHub

何为 GitHub？

[GitHub](https://github.com) 是一个源代码托管平台。呃呃，没了（（（

当你兴致勃勃地打开时，发现：此网站响应时间过长（（（

于是，加速器就来了。

有两种选择：

### [Watt Toolkit (原 Steam++)](https://steampp.net)

这是一个加速器，主要目的其实是加速 Steam 的，但是还附带了 GitHub 和 Vercel，功能已经很丰富了 QwQ

安装完毕后开启 GitHub 和 vercel.app 即可开启加速。

### [Geph (中文名迷雾通)](https://geph.io/zhs)

更准确地说，这不属于加速器，而是一个翻墙软件。

它提供完整外网环境，免费版已经够用了

---

网上的教程大多数是在本地搭建博客，而这个文章介绍的是将博客搭建在 GitHub 上。

---

### GitHub Codespaces

这玩意是 GitHub 被 Microsoft 买了之后（应该是吧）推出的功能，用户可以在有一个仓库的前提下在 Codespaces 上修改仓库文件和提交更改。

## [Hexo](https://hexo.io/zh-cn)

Hexo 是一个博客框架，基于 Node.js。呃呃，又没了（（（

## [Vercel](https://vercel.com)

这个网站是后面 Qexo 需要用到的源代码部署网站，一样需要加速器。

## [Qexo](https://www.oplog.cn/qexo/start.html)

这是一个后端，这篇文章就是用它写出来的 QwQ

## [Cloudflare](https://cloudflare.com)

这是一个强大的网站，可以帮助提升个人域名的安全性，还有免费的 DNS 和免费的证书。总之歪瑞Biu特否就对了QwQ

## [硅云](https://vpsor.cn)

领先的出海 IaaS 云计算基础设施提供商。

我们用它来注册域名。

至于为什么不是阿里云、腾讯云等常见的提供商……

因为硅云可以免费白嫖一年的域名 OvO!!

---

东西都介绍完了，接下来开始

# 搭建博客

首先，注册一个 GitHub，打开网站点 Sign Up 按照提示一步步注册就行了。

然后新建一个仓库，用来存储 Hexo 静态文件，注意选 Private 以及 Add README。

![](https://cdn.luogu.com.cn/upload/image_hosting/p7kdm3or.png)

打开后，点击 Code -> Codespace -> Creat Codespace on master，等一小会，你可以去饭(ce)馆(suo)加个餐，Codespaces 就会初始化完成。

![](https://cdn.luogu.com.cn/upload/image_hosting/x3jvh4gp.png)

在终端输入：

```bash
$ npm i hexo-cli -g
```

就可以把基本框架 Hexo 安装好。

然后在根目录执行：

```bash
$ hexo init 123456
```

完成后把 123456 文件夹内的文件全部移动到根目录下，把 123456 删除，README 也可以删了。

现在，就可以

```bash
$ hexo g && hexo s
```

来看看效果了 QwQ

出现一个 `localhost:4000` 时，不要直接复制访问，要按 Ctrl+左键打开。

![](https://cdn.luogu.com.cn/upload/image_hosting/dwvzhhwz.png)

哇，真鬽櫪(meili)

到这里，博客搭建就完成啦 QwQ!!

但是，这个主题太丑了，所以我们要

# 更换主题

打开 `https://hexo.io/themes`，可以看到上面有很多主题，个人推荐要实用的就 NexT 了，好看点的就 Redefine。

至于配置……自己看文档去（（（

拿 Redefine 举例，
