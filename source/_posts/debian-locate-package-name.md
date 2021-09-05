---
title: Debian查找包含某个文件或程序的deb包名
date: 2020-09-05 06:29:15
tags:
---
## 使用`dpkg`

```bash
dpkg -S /usr/bin/passwd
```
<!--more-->
## 使用 `dpkg-query`

```bash
dpkg-query -S '/bin/ls'
dpkg-query -S 'passwd*'
dpkg-query --search '/path/to/file'
dpkg-query --search '/usr/bin/passwd'
dpkg-query --search '/etc/passwd'
```

## 使用 `apt-file`

第一次使用要先安装`apt-file`, 然后更新其数据库:

```bash
sudo apt-get install apt-file
sudo apt-file update
```

使用命令查询文件所在的包:

```bash
apt-file search date
apt-file search kvm-ok
```
