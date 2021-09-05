---
title: VS Code 搭建 Linux Kernel 开发环境 - 2021版
date: 2021-09-05 16:19:38
tags:
---

VS CODE 是当今最流行的IDE之一, 其丰富的插件功能使得VS CODE可以面向多种应用场景.

本文介绍一种利用VS CODE的`C/C++ Intellisense`插件 + [vscode-linux-kernel](https://github.com/amezin/vscode-linux-kernel)搭建的Linux kernel开发环境.

此方案个人认为是迄今为止（2021年9月份）最好用的`VS CODE`内核开发IDE方案, 特别是如果你还没有跳入`vim`或者`emacs`的坑的话.
<!--more-->
## 操作系统环境

本文使用的是`Ubuntu/Debian`系操作系统. 理论上`Windows`也可以使用此方案，不过考虑到`Windows`文件系统的特性，理论上应该没有`Linux`上的好用。

## 安装 `VS Code`

这句是废话.

## 安装 `C/C++ Intellisense`

插件商店直接安装，略过.

## 下载安装内核源码并执行一次编译

此步骤是为了得到内核编译之后的[`*.o.cmd`](https://unix.stackexchange.com/questions/186577/what-are-the-some-name-o-cmd-files)文件, 此文件内包含编译对应的目标文件需要的编译命令, 源文件和包含的头文件.

## 安装`vscode-linux-kernel`

在编译后的内核源码根目录下, 删除或者备份`.vscode`目录，然后执行:

```bash
git clone git@github.com:amezin/vscode-linux-kernel.git .vscode
```

如果你内核的目标平台不是`x64`, 需要修改`.vscode/c_cpp_properties.json`里的`intelliSenseMode`为:

- `gcc-x86`
- `gcc-x64`
- `gcc-arm`
- `gcc-arm64`

中的一个. 例如你编译内核的目标平台是`arm64`的开发板, 就需要把它改为`gcc-arm64`.

## 生成编译命令数据库

```bash
python .vscode/generate_compdb.py
```

这一步执行完毕之后,会在内核代码数的根目录生成`compile_commands.json`文件,此文件中记录了编译内核时所需要的所有编译命令. 然后这个文件通过`c_cpp_properties.json`的`compileCommands`选项传给`C/C++ Intellisense`插件, 后续插件会根据此数据库构建它的代码分析和跳转.

## 启动`VS Code`

```bash
code .
```

IDE启动后会花一些时间来同步后台数据库, 你会在`Code`的状态栏看到一个圆柱形的图标, 把鼠标悬停在上面可以显示进度.

根据CPU和磁盘的性能, 此阶段所花费的时间不等. 根据我个人的经验, 单核1.8G的CPU,SATA SSD的情况下, 大概要花费20分钟.

同步结束之后, 就可以愉快的使用跳转和搜索引用的功能了.

### 搜索引用

鼠标右键点击任意函数或者变量, 然后选择`Find All Reference[Alt+Shift+F12]`查找引用. 结果会显示在左侧, 一共有两块:

- `REFERENCE`
- `C/C++: 其他引用结果`

上面的那块也可以称之为`活动引用`. 一般来言, `活动引用`是最需要关注的, 因为这些引用是通过参与编译的文件搜索出来的.

下面的那块包含了非活动引用, 注释引用, 字符串引用等.

## `Out-of-tree` 编译的内核或者模块

`Out-of-tree`编译的内核和模块的配置与上面的配置大同小异, 请直接参考官方文档[Out-of-tree builds](https://github.com/amezin/vscode-linux-kernel#out-of-tree-builds)和[Out-of-tree module development](https://github.com/amezin/vscode-linux-kernel#out-of-tree-module-development), 此处不再赘述.
