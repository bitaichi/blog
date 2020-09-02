---
title: 搭建Firebase托管的HEXO
date: 2020-09-02 22:22:46
tags:
---

此博客使用HEXO搭建, 代码托管在github, 网站托管在Firebase平台的免费计划, 利用Github的Action自动更新Firebase的托管网站.

## 开发环境

本文的开发环境为一台`Debian Buster`虚拟机. 鉴于国内网络无法访问`Firebase`平台, 需要准备一个代理.

## 安装依赖

主要依赖为`Node.js`和`git`.

`node`的安装参考: [nvm](https://github.com/nvm-sh/nvm)

`git`的安装:

```bash
sudo apt update
sudo apt install git
```

国内安装完后最好配置下`git`的代理.

## 安装Hexo

```bash
https_proxy=http://PROXY_IP:PORT/ npm install -g hexo-cli
```

## 建立Hexo工程

```bash
https_proxy=http://PROXY_IP:PORT/ hexo init blog
cd blog
https_proxy=http://PROXY_IP:PORT/ npm install
```

然后使用`hexo server`命令测试本地工程. 然后, 使用`hexo generate`生成网站.

## 上传Hexo工程到github

在github上创建一个项目, 然后将工程上传.

## 创建Firebase项目

登录到`Firebase`[控制台](https://console.firebase.google.com/), 点击`创建项目`:

![](01.png)

输入项目名称, 然后点击继续:

![](02.png)

设置GA:

![](03.png)

![](04.png)

## 安装配置firebase cli

### 安装:

```bash
https_proxy=http://PROXY_IP:PORT npm install -g firebase-tools
```

### 登录:

```bash
https_proxy=http://PROXY_IP:PORT firebase login --no-localhost
```

此步会输出一个网址, 复制这个URL, 在浏览器中打开, 登录google账号, 获取一段授权代码, 并粘贴到shell. 成功后会显示`Success! Logged in as xxx@gmail.com`

### 初始化:

```bash
https_proxy=http://PROXY_IP:PORT firebase init
```

这一步选择`Hosting`, space键选中, 回车键确认:

![](05.png)

项目设置, 选择使用现有的项目, 然后选中之前在firebase控制台创建的项目:

![](06.png)
![](07.png)

托管设置, 选择默认值即可, 分别为`public`和`No`.

![](08.png)

这一步执行完之后, 会多出两个文件`.firebaserc`和`firebase.json`, 提交他们.

### 测试部署:

```bash
https_proxy=http://PROXY_IP:PORT firebase deploy
```

结果如图:

![](09.png)

此时打开[https://bitaichiblog.web.app](https://bitaichiblog.web.app)查看效果.

## 配置GitHub Action

### 获取Firebase CI Token

这一步配置github的action, 使得我们每次对maste分支的提交都会自动更新到firebase上.

Firebase的部署需要一个`Token`, 首先让我们获取它, 在工程根目录执行:
```bash
https_proxy=http://PROXY_IP:PORT firebase login:ci --no-localhost
```

和上面的登录步骤很像, 此时控制台会打印出一个URL, 复制它, 然后在浏览器中打开, 登录google账号, 获取授权代码, 然后粘贴回控制台. 最后执行成功后, 控制台打印出下一步需要的Token, 记下来. 执行截图如下:

![](12.png)

Github项目页面点击`Settings` - `Secrets` - `New secret`

![](13.png)

Name填入`FIREBASE_TOKEN`, Value为刚才获取到的token值:

![](14.png)

### 创建Action

首先创建一个空白的`Action`:

![](10.png)

然后, 将文件命名为`deploy2firebase.yml`:

![](11.png)

复制粘贴如下代码, 然后点击右上角的`Start commit`提交代码.
```yaml
name: Deploy to Firebase

on:
  push:
    branches: [ master ]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Install Dependencies
        run: npm install

      - name: Build
        run: npm run build

      - name: Deploy to Firebase
        uses: w9jds/firebase-action@master
        with:
          args: deploy --only hosting
        env:
          FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
```

提交后, 在`Action` - `Workflows` 下点击 `Deploy to Firebase`, 查看执行状态. 具体job的执行状态可以点击job名字进去查看:

![](15.png)

Workflow执行完毕之后, Firebase上的站点就更新了.

后续的工作流就是用hexo写文章, 写完之后提交commit到Github, 然后会自动触发Action, 从而自动更新Firebase托管的页面.
