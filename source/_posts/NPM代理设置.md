---
title: NPM代理设置
date: 2020-09-05 07:17:42
tags:
---

## 设置代理

```bash
npm config set proxy http://<username>:<password>@<proxy-server-url>:<port>
npm config set https-proxy http://<username>:<password>@<proxy-server-url>:<port>
```

## 取消代理

任选一个方法取消代理, 如果一个方法不行, 尝试另外一个.

```bash
# way 1
npm config delete http-proxy
npm config delete https-proxy

# way 2
npm config rm proxy
npm config rm https-proxy

# way 3
set HTTP_PROXY=null
set HTTPS_PROXY=null
```
