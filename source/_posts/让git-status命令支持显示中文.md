---
title: 让git status命令支持显示中文
date: 2020-09-05 07:46:58
tags:
---

执行下面的命令

```bash
git config --global core.quotepath false
```

然后就可以显示中文了
<!--more-->
```bash
git status
```
