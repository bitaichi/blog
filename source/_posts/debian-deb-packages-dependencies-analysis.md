---
title: debian deb packages dependencies analysis
date: 2020-09-05 06:36:38
tags:
---
# 分析deb包依赖

## 使用`apt-rdepends`

输出.dot文件

然后利用图形化工具转换成png文件

```bash
sudo apt install apt-rdepends xdot
```

```bash
apt-rdepends -d libegl1 | dot -Tsvg > libegl1-map.svg
```

## 使用`apt-cache`

这个方法应该是目前的主流方法

列出直接依赖

```bash
apt-cache depends apt
```

输出dot格式

```bash
apt-cache dotty apt
```

反向依赖

```bash
apt-cache rdepends apt
```
