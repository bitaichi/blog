---
title: VS CODE + GNU GLOBAL 搭建kernel开发环境
date: 2020-09-05 07:15:54
tags:
---

VS CODE 是当今最流行的IDE之一, 其丰富的插件功能使得VS CODE可以面向多种应用场景.

本文介绍一种利用VS CODE的`C/C++ Intellisense`插件 + GNU Global的tags系统搭建的Linux kernel开发环境.
<!--more-->
[GNU Global](https://www.gnu.org/software/global/)是GNU的一个开源项目, 他是一个tagging系统; [C/C++ Intellisense](https://marketplace.visualstudio.com/items?itemName=austin.code-gnu-global)是利用这一开源tagging工具的VS CODE插件.

下面是搭建步骤

## 安装GNU Global - Windows

GNU Global官方并不提供windows平台下的二进制, 但是第三方有人已经打包了, 地址在[GLOBAL](http://adoxa.altervista.org/global/)

Windows系统下, 访问此网站下载二进制包, 然后解压到任一目录, 并将其`bin`目录加到`PATH`里.

## 安装GNU Global - Debian

```bash
sudo apt update
sudo apt install global
```

## 安装C/C++ Intellisense

在VS CODE的插件页面搜索`C/C++ Intellisense`并安装之, 然后使能到workspace.

关闭自动保存时候的自动更新, `Preference - User Settings`里`"codegnuglobal.autoupdate": false`.

## 创建tagging数据库

在项目根目录下输入

```bash
gtags
```

会生成`GTAGS`, `GRTAGS`, 和`GPATH`等文件.

## 更新tagging数据库

每次修改代码后, 键入

```bash
global -u
```

来更新数据库.
