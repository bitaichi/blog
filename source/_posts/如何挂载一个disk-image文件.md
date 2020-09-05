---
title: 如何挂载一个disk image文件
date: 2020-09-05 06:54:33
tags:
---

在嵌入式Linux的开发和使用中, 磁盘镜像文件经常会用到, 而有的时候需要调查一下磁盘镜像文件系统中的内容, 但是手头有没有设备.

这种时候可以考虑直接将磁盘镜像挂载为loopdevice, 然后就可以直接访问其内容了. 方法如下:

一般的磁盘镜像文件都是整个磁盘的镜像, 而Linux的挂载是基于分区的, 所以, 首先要做的事情是搞清楚磁盘镜像文件里究竟有哪些分区.

```bash
$ fdisk -l ./bbx15deb.img
Disk ./bbx15deb.img: 3.53 GiB, 3774873600 bytes, 7372800 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xf7146e7c

Device              Boot Start     End Sectors  Size Id Type
./bbx15deb.img1 *     8192 7372799 7364608  3.5G 83 Linux
```

这里面需要的信息:
- 块大小: 512 字节
- 分区开始区块: 8192

所以, 这里需要挂载的分区是`bbx15deb.img1`, 分区的起始地址是 8192*512 = 4,194,304 字节.

然后开始创建挂载的目录:

```bash
sudo mkdir -p ./temp
```

执行挂载

```bash
sudo mount -o loop,offset=4194304 ./bbx15deb.img ./temp
```

然后进入temp目录查看内容.

在查看完毕之后, 执行命令卸载:

```bash
sudo umount ./temp
```
