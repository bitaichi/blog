---
title: Docker proxy
date: 2020-09-05 07:21:00
tags:
---
有两种不同的docker代理:

- 用来访问docker registry的, 通常用来下载docker镜像, 例如访问`hub.docker.com`.
- 容器内使用的代理.

## docker registry 代理

参考 [HTTP/HTTPS proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)

## 容器内使用的代理

参考 [Configure Docker to use a proxy server](https://docs.docker.com/network/proxy/)
