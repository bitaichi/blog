---
title: KDE桌面环境中文输入配置
date: 2020-09-05 07:12:08
tags:
---

KDE Plasma桌面环境下的中文输入配置的文章不太多, 这里总结一下Debian 10 Buster上的KDE的中文输入法配置, 其他发行版的KDE上的配置方式也应该差不多
<!--more-->
## 配置locales

输入命令配置中文locales:

```bash
sudo dpkg-reconfigure locales
```

在locales选择界面拉到最下面, 选择所有的中文相关的locales(以`zh_CN`开头的那些). 然后在下一个界面选择`en_US.UTF-8`作为系统的默认locale.

## 安装`fcitx`框架及拼音输入法

```bash
sudo apt update
sudo apt install kde-config-fcitx fcitx-sunpinyin
```

安装完毕后重启系统.

## 配置`fcitx`输入法

打开输入法配置工具:

```bash
fcitx-configtool
```

先检查一下`Sunpinyin`是否在右侧的当前输入法列表中, 如果没有, 取消勾选`Only Show Current Language`, 然后在搜索框内输入`pinyin`, 选中`Sunpinyin`, 将其加入右侧的当前输入法列表中. 保存关闭.

使用快捷键`Ctrl + Space`切换输入法, 现在应该可以输入中文了.
