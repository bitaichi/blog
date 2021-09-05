---
title: Shell 美化
date: 2020-09-05 06:57:55
tags:
---

## 使用Powerline
<!--more-->
首先安装

```bash
sudo apt install powerline
```

然后修改`~/.bashrc`文件使能之:

```bash
if [ -f /usr/share/powerline/bindings/bash/powerline.sh ]; then
    source /usr/share/powerline/bindings/bash/powerline.sh
fi
```

最后重启shell查看效果.

如果字体存在乱码, 需要安装支持PL的字体, 见下面的命令, 没有测试过.

```bash
# clone
git clone https://github.com/powerline/fonts.git --depth=1
# install
cd fonts
./install.sh
# clean-up a bit
cd ..
rm -rf fonts
```

如果git信息不显示, 将如下设置添加到`~/.config/powerline/config.json`

```json
{
    "ext": {
        "shell": {
            "theme": "default_leftonly"
        }
    }
}
```

## bash补全历史命令

创建文件`~/.inputrc`, 输入内容

```plain
"\e[5~": history-search-backward
"\e[6~": history-search-forward
```

然后使用`PageUp`和`PageDown`来查找历史命令.

## 历史命令自动补全

安装fish

```bash
sudo apt install fish
```

添加下列行到`~/.config/fish/config.fish`

```bash
set fish_function_path $fish_function_path "/usr/share/powerline/bindings/fish"
source /usr/share/powerline/bindings/fish/powerline-setup.fish
powerline-setup
```

输入fish启动.
