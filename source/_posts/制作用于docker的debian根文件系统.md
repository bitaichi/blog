---
title: 制作用于docker的debian根文件系统
date: 2020-09-05 06:51:53
tags:
---

在制作基于debian的docker镜像的时候, 一般可以从`hub.docker.com`选择合适的版本.

但是在某些时候, 我们可能需要特定版本的debian系统作为制作docker镜像的基础发行版, 此时现有的版本就无法满足我们的需求.

所以, 为了解决这种需求, 需要自己制作debian的基础镜像包. 其实方法也很简单, 官方提供了制作debian基础镜像包的工具, 而且, `hub.docker.com`上的官方镜像也是用相同的工具制作出来的.

下面我们就来看看如何基于官方提供的工具制作自己的debian基础镜像包吧.

本教程使用的操作系统是Ubuntu 20.04. 理论上, 可以使用任何基于Debian/Ubuntu的系统.

## 安装`debuerreotype`

官方制作用于docker的debian根文件系统的工具是[debuerreotype](https://github.com/debuerreotype/debuerreotype).

安装方式:

```bash
sudo apt update
sudo apt install debuerreotype
```

## 创建根文件系统

接下来, 我们创建一个工作目录, 制作后的根文件系统会存在与这个目录:

```bash
mkdir -p ./workspace
cd ./workspace
```

然后, 执行`debuerreotype-init`命令来初始化根文件系统.

```bash
$ sudo debuerreotype-init rootfs  buster 2020-01-17T15:04:11Z
I: Retrieving InRelease
I: Checking Release signature
I: Valid Release signature (key id 6D33866EDD8FFA41C0143AEDDCC9EFBF77E11517)
...
I: Checking component main on http://snapshot.debian.org/archive/debian/20200117T150411Z...
...
I: Base system installed successfully.
```

这里需要提供几个参数, 分别是:

- 存放根文件系统的目录, 本文中设置的是当前目录下的`rootfs`目录, 制作好的根文件系统就存在于这个目录中.
- 发行版本, 这里使用`buster`, 即`Debian 10`.
- debian快照时间戳. `debuerreotype`工具的原理是从debian快照仓库`snapshot.debian.org`中获取某个时间的快照的软件包版本, 所以, 这里需要提供时间戳参数. 制作后的根文件系统中的软件都会从这个时间戳指定的版本库中下载.

根文件系统制作完成之后, 执行下面命令检查epoch:

```bash
$ cat rootfs/debuerreotype-epoch
1579273451
```

下一步, 需要配置一下根文件系统, 以缩小其footprint, 以便于docker使用:

```bash
sudo debuerreotype-minimizing-config rootfs
```

如果制作出来的根文件系统缺少某些自己需要的包, 可以通过下面的命令安装:

```bash
sudo debuerreotype-apt-get rootfs update -qq
sudo debuerreotype-apt-get rootfs install -yqq --no-install-recommends inetutils-ping iproute2
```

接下来, 需要更新一下根文件系统中的apt的source list:

```bash
sudo debuerreotype-debian-sources-list rootfs buster
```

最后, 将制作好的根文件系统打包, 并清理掉`rootfs`目录:

```bash
sudo debuerreotype-tar rootfs rootfs.tar.xz
sudo rm -rf rootfs
```

我们的根文件系统就制作好了, 接下来, 就是用它来创建docker基础镜像了.

## 创建docker基础镜像

还是在之前的目录中, 创建`Dockerfile`:

```Dockerfile
FROM scratch
ADD rootfs.tar.xz /
CMD ["bash"]
```

然后, 编译docker镜像, 为此镜像打上`debian-buster:v20200117`的tag:

```bash
docker build . -t debian-buster:v20200117
```

OK, 基础镜像就制作完了.

## 使用docker基础镜像

直接在要使用的地方创建`Dockerfile`并导入制作的基础镜像:

```Dockerfile
FROM  debian-buster:v20200117
```

对于`debian`镜像, 有一些通用配置, 下面的例子给出了一些典型的通用配置, 可以拿这个例子作为基础制作自己的镜像:

```Dockerfile
# 导入系统底包, 这里既可以使用自己制作的底包, 也可是使用hub上的版本
FROM  debian-buster:v20200117

# 设置locale
RUN apt-get update && \
    apt-get install -y locales && \
    localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG=en_US.utf8

# [可选] 添加其他架构支持:
RUN dpkg --add-architecture arm64

# 更新并安装一些工具包:
RUN apt-get update && \
    apt-get install -y git build-essential sudo && \
    apt-get clean

# 设置默认sh为bash:
RUN echo "dash dash/sh boolean false" | debconf-set-selections
RUN dpkg-reconfigure dash

# 添加新用户 build , 并加入sudo用户组, 配置无密码sudo访问:
RUN adduser --disabled-password --gecos '' build && \
  usermod -aG sudo build && \
  echo "build ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# 配置用户
USER build
ENV HOME /home/build
ENV LANG en_US.UTF-8
RUN echo 'alias ll="ls -alh"' >> ~/.bashrc

# 设置工作目录
WORKDIR /home/build
```

OK, `Dockerfile`写好之后, 执行`docker build`编译镜像. 然后使用下面的命令来临时使用镜像:

```bash
docker run -it --rm <image name> /bin/bash
```

注意, 上面命令带有`--rm`参数, 所以容器退出之后会自动删除自己, 不要在此容器内执行需要保存的工作. 若有需要保存的内容, 请使用`-v`参数指定一个映射.

## 参考

- [debuerreotype](https://github.com/debuerreotype/debuerreotype)
