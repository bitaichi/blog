---
title: How to use aria2
date: 2020-09-05 21:34:06
tags:
---

## Docker

[docker-aria2-pro](https://p3terx.com/archives/docker-aria2-pro.html)

```bash
docker run -d \
    --name aria2-pro \
    --restart unless-stopped \
    --log-opt max-size=1m \
    -e PUID=$UID \
    -e PGID=$GID \
    -e RPC_SECRET=YOUR_RPC_SECRET \
    -p 6800:6800 \
    -p 6888:6888 \
    -p 6888:6888/udp \
    -v /path/to/aria2/config:/config \
    -v /path/to/aria2-downloads:/downloads \
    p3terx/aria2-pro
```

## WEBUI

[aria2-frontend-ariang-tutorial](https://p3terx.com/archives/aria2-frontend-ariang-tutorial.html)

使用 [AriaNg](https://github.com/mayswind/AriaNg/releases) 中的 AllInOne版本
