---
title: hexo博客搭建
date: 2020-02-10 22:07:02
updated: 2020-02-10 22:07:02
tags: 
- 博客搭建
---

本文用于记录我在创建hexo博客时的方法和步骤。
这是我第一次创建自己的博客，使用hexo主要是因为他的创建方法很简单，还可以通过git部署到公网。
这篇文章大部分参考了hexo的 [官方文档](https://hexo.io/zh-cn/docs/) 所有内容在官方文档中都有更加详细的说明。

## Hexo
Hexo 是一个快速、简洁且高效的博客框架。hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
[官网](https://hexo.io/zh-cn/)

## 安装Hexo
在你要存放博客的文件夹下使用命令窗口输入:
```
$ npm install -g hexo-cli
```
hexo是机遇 [Node.js](https://nodejs.org/en/download/) 的，所以在安装hexo之前我们需要先安装好Node.js
因为我们后面要使用git将我们的博客部署到公网所以也需要先把 [git](https://git-scm.com/download/win) 安装好
>使用Node.js官方安装程序时，请确保勾选 Add to PATH 选项（默认已勾选）

## 建站
```
$ hexo init
$ npm install
```
这个过程需要一些时间
创建好后会得到这样的文件结构
```
.
|——_config.yml  # 网站的配置信息
|——package.json  # 应用程序信息
|——scaffolds  # 模板文件夹
|——source  # 资源文件夹
|  |__ _posts  # 文章文件夹
|__themes  # 主题文件夹
```
我们可以在 [_config.yml](https://hexo.io/zh-cn/docs/configuration) 中修改大部分的配置
到这里我们的hexo博客就建好了，接下来就是将我们的博客部署到公网上了。这部分的内容放到之后再更新上来。