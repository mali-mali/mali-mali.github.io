---
title: hexo博客搭建及git部署
date: 2020-02-10 22:07:02
updated: 2020-02-17 19:56:00
tags: 
- 博客搭建
- git同步
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
到这里我们的hexo博客就建好了，接下来就是将我们的博客部署到公网上了。

## 博客部署到公网
关于如何将博客部署到公网，这里就要用到我们强大的git了。

首先我们需要在git上创建一个新仓库。
>注意: 仓库名必须是 [你的github用户名.github.io]

然后我们需要生成ssh秘钥文件，因为Hexo部署在github上是通过密钥配对上传的。
Windows搜索打开Git Bash 输入
```
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"

ssh-keygen -t rsa -C "你的GitHub注册邮箱"

cd ~/.ssh
```
Windows密钥文件在C:/Users/你的用户名/.ssh目录下。
打开git个人设置-SSH and GPG keys 页面- New SSH key 将刚刚复制的id_rsa.pub内容粘贴到key，最后点击Add SSH key。

### 网站部署
设置好git仓库后还需要修改hexo的配置文件，修改_config.yml
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/你的github用户名/你的github用户名.github.io.git
  branch: master

```

修改好后使用
```
hexo d
```
既可以在公网上通过 [你的github用户名.github.io] 看到你的个人博客啦。

## git同步管理
既然我们使用了git部署个人博客，那么能不能使用git来做博客的同步管理呢？
当然是可以的。
在博客根目录下打开Git Bassh
```
git init #git初始化
git remote add origin https://github.com/你的GitHub用户名/你的GitHub用户名.github.io.git #添加仓库地址
git checkout -b 分支名 #新建分支并切换到新建的分支
git add . #添加所有本地文件到git
git commit -m "这里填写你本次提交的备注" #git提交
git push origin 分支名 #文件推送到hexo分支
```
比如这样：
```
git init #git初始化
git remote add origin https://github.com/xxx/xxx.github.io.git #添加仓库地址
git checkout -b hexo #新建分支并切换到新建的分支
git add . #添加所有本地文件到git
git commit -m "我的hexo同步文件" #git提交
git push origin hexo #文件推送到hexo分支
```
这样就把博客项目上传到了git仓库的hexo分支下

### 在另一台电脑下载博客工程
```
git clone -b 分支名 https://github.com/你的GitHub用户/你的GitHub用户.github.io.git
```
克隆下载完成后，进入到你项目的文件夹，配置你的hexo环境
```
npm install -g hexo-cli #安装hexo,注意这里不需要hexo初始化,否则之前的hexo配置参数会重置
npm install #安装依赖库
npm install hexo-deployer-git #安装git部署相关配置
```
>这里同样是要使用到ssh秘钥的，生成方式同上

### 文件的上传和更新
```
git add .
git commit -m "这里填写你本次提交的备注"
git push origin 分支名
```
需要更新git上的项目文件时只需要执行上面的代码即可

同步只需要执行
```
git pull
```

到这我们不仅将博客部署到了公网上，还能够通过git进行同步管理啦。