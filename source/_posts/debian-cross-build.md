---
title: Debian安装交叉编译工具链
date: 2020-09-05 06:25:32
tags:
---
## 方法

主要是通过安装`crossbuild-essential-arch`来实现

```bash
sudo apt install crossbuild-essential-arm64
```

一些交叉编译的工具链:

- crossbuild-essential-amd64
- crossbuild-essential-arm64
- crossbuild-essential-armel
- crossbuild-essential-armhf
- crossbuild-essential-i386
- crossbuild-essential-mips
- crossbuild-essential-mips64el
- crossbuild-essential-mipsel
- crossbuild-essential-powerpc
- crossbuild-essential-ppc64el
- crossbuild-essential-s390x

然后运行相应的`gcc`或者`g++`程序, 例如:

```bash
/usr/bin/i686-linux-gnu-gcc hello.c
```

可以用`file`命令来检查目标文件的架构:

```bash
sudo apt install file
file a.out
```

## Refer

- [ToolChain/Cross](https://wiki.debian.org/ToolChain/Cross)
