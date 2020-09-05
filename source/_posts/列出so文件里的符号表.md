---
title: 列出so文件里的符号表
date: 2020-09-05 06:55:27
tags:
---
## 使用`nm`命令来达到目标

```bash
nm -gD yourLib.so
```

## 如果目标so文件是C++库, 添加`-C`选项

```bash
nm -gDC yourLib.so
```

## 如果目标so文件是`elf`格式

- 使用`objdump`:

    ```bash
    objdump -TC libz.so
    ```

- 使用`readelf`:

    ```bash
    readelf -Ws libz.so
    ```
