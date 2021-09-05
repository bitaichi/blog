---
title: Git仓库路径操作
date: 2020-09-05 07:23:23
tags:
---

## 显示当前仓库的根目录的绝对路径

After 1.7.x:
<!--more-->
```bash
git rev-parse --show-toplevel
```

Before 1.7.x:

```bash
git rev-parse --git-dir
```

That would give the path of the `.git` directory.

## 显示当前目录相对于仓库根目录的相对路径

如果在仓库根目录下执行的话, 返回空.

```bash
git rev-parse --show-prefix
```

## 检查当前目录是否属于git仓库

```bash
git rev-parse --is-inside-work-tree
```

## 检查当前目录是否属于`.git`目录下

```bash
git rev-parse --is-inside-git-dir
```
