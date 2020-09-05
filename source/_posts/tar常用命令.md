---
title: tar常用命令
date: 2020-09-05 07:14:38
tags:
---

## 压缩

### GZIP (tar.gz, tgz)

```bash
tar -czvf name-of-archive.tar.gz /path/to/directory-or-file /path2 --exclude=/path3
```

- `-c`: Create an archive
- `-z`: use gZip
- `-v`: Verbose
- `-f`: Filename of the archive
- `--exclude`: exclude paths

### BZIP2 (tar.bz, tar.bz2, tbz)

和GZIP一样, 除了把`-z`换成`-j`.

```bash
tar -cjvf name-of-archive.tar.gz /path/to/directory-or-file /path2 --exclude=/path3
```

### XZ (tar.xz)

和GZIP一样, 除了把`-z`换成`-J`.

```bash
tar -cJvf name-of-archive.tar.gz /path/to/directory-or-file /path2 --exclude=/path3
```

## 解压

### GZIP

```bash
tar -xzvf archive.tar.gz -C /tmp
```

- `-x`: eXtract
- `-z`: gzip
- `-v`: Verbose
- `-f`: Filename of the archive
- `-C`: destination folder

### BZIP2

和GZIP一样, 除了把`-z`换成`-j`.

```bash
tar -xjvf archive.tar.gz -C /tmp
```

### XZ

和GZIP一样, 除了把`-z`换成`-J`.

```bash
tar -xJvf archive.tar.gz -C /tmp
```
