---
title: 如何添加新的存储空间到Ubuntu的LVM
date: 2020-09-05 06:57:17
tags:
---

## 创建 PV

首先，对新添加的存储空间做分区, 根据自己情况选择合适的/dev/sdx设备：
<!--more-->
```bash
sudo fdisk /dev/sda
```

然后把新添加的空间创建成0x8e类型的分区，一般是输入n, p， 分区号，开始扇区，结束扇区，然后输入t，选择分区，输入8e，然后w保存.

使用命令查看新添加的分区：

```bash
sudo fdisk -l /dev/sda
```

然后创建pv:

```bash
sudo pvcreate /dev/sdax
```

`/dev/sdax` 为刚才新创建的分区.

然后验证新创建的分区:

```bash
sudo pvs
```

## 将新创建的 PV 添加到 VG

```bash
sudo vgextend vg_name /dev/sdax
```

`vg_name`为VG的名字, `/dev/sdax`为新创建的分区.

验证:

```bash
sudo vgs
sudo pvscan
```

## 扩展LV的空间

执行下面命令获取VG的可用空间, 即 `Free PE`:

```bash
$ sudo vgdisplay
  --- Volume group ---
  VG Name               ubuntu-mate-vg
  System ID             
  Format                lvm2
  Metadata Areas        3
  Metadata Sequence No  6
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                3
  Act PV                3
  VG Size               99.99 GiB
  PE Size               4.00 MiB
  Total PE              25598
  Alloc PE / Size       15348 / 59.95 GiB
  Free  PE / Size       10250 / <40.04 GiB
  VG UUID               TkawbL-i8BO-V0MF-oW3D-uZi9-j6W4-XyCOBH
```

可以看到`Free PE` 为 `10250`

执行`lvdisplay`来获取LV的名字:

```bash
$ sudo lvdisplay
  --- Logical volume ---
  LV Path                /dev/ubuntu-mate-vg/root
  LV Name                root
  VG Name                ubuntu-mate-vg
  LV UUID                sLhWW9-ZKWT-rm3A-gYUJ-w2KV-JeA0-6PWVvp
  LV Write Access        read/write
  LV Creation host, time ubuntu-mate, 2018-10-19 17:06:14 +0800
  LV Status              available
  # open                 1
  LV Size                59.00 GiB
  Current LE             15104
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/ubuntu-mate-vg/swap_1
  LV Name                swap_1
  VG Name                ubuntu-mate-vg
  LV UUID                0xPocl-PzMn-qNZ0-g5v9-33pg-ItsK-aFHyqQ
  LV Write Access        read/write
  LV Creation host, time ubuntu-mate, 2018-10-19 17:06:14 +0800
  LV Status              available
  # open                 2
  LV Size                976.00 MiB
  Current LE             244
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1
```

可以看到目标LV的名称为`root`, 即我们要扩大的LV, 其路径为 `/dev/ubuntu-mate-vg/root`

然后执行命令来扩展LV空间:

```bash
sudo lvextend -l +10250 /dev/ubuntu-mate-vg/root
```

然后resize 扩展后的LV:

```bash
sudo resize2fs /dev/ubuntu-mate-vg/root
```

## 验证结果

使用如下命令来查看:

```bash
sudo pvs
sudo vgs
sudo lvs
sudo pvdisplay
sudo lvdisplay
sudo vgdisplay
```

## 非LVM的磁盘扩容

高风险操作, 切记提前备份数据.

首先使用`fdisk`检查磁盘分区, 将要扩容后的分区的其他分区都删掉, 然后记下扩容分区的起始扇区号.

然后使用`fdisk`, 先删除目标分区, **不要保存**, 然后添加新分区, 起始扇区为刚才记下的扇区号, 结束扇区为最大, 如果遇到询问"已经有ext4的signature信息, 要不要删除", 一律选NO, 然后保存退出.

然后刷新分区信息:`partprobe /dev/sdX`

然后使用`resize2fs /dev/sdX`进行文件系统扩容.

最后检查一下.
