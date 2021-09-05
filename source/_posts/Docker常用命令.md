---
title: Docker常用命令
date: 2020-09-05 07:22:04
tags:
---

## attach到一个正在运行的容器上
<!--more-->
- `docker exec -it <container name> /bin/bash`
- `docker exec -it <container name> <command>`

## 在一个新容器上执行bash

- `docker run -it --rm <image name> /bin/bash`
