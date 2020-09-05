---
title: apt proxy
date: 2020-09-05 06:49:17
tags:
---

Add file `99proxy`:

```bash
Acquire {
	http {
		Proxy "http://192.168.100.105:6002/";
		Proxy::mirrors.huaweicloud.com "DIRECT";
	};
};
```

to `/etc/apt/apt.conf.d/`
