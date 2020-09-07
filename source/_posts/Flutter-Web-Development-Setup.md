---
title: Flutter Web 开发环境搭建
date: 2020-09-06 23:37:44
tags:
---

操作系统为Debian Buster

## 安装Android Studio

首先安装一些依赖包:

```bash
sudo dpkg --add-architecture i386
sudo apt update
sudo apt install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386
```

然后, 从[这里](https://developer.android.com/studio#downloads)下载Linux 64bit的Android Studio

解压缩下载下来的解压包到用户目录.

```bash
tar -xzvf android-studio-ide-193.6626763-linux.tar.gz -C ~/
```

然后运行:

```bash
cd ~/android-studio/bin
./studio.sh
```

按照向导完成配置, 进入Andriod Studio欢迎界面, 点击右下角的`Configure` - `Plugins`, 搜索并安装`Flutter`插件.

## 安装Flutter SDK

参考[这里](https://flutter.dev/docs/get-started/install/linux)

安装依赖

```bash
sudo apt update
sudo apt install curl file git unzip zip xz-utils libglu1-mesa
```

克隆`flutter`仓库

```bash
cd ~/
git clone https://github.com/flutter/flutter.git
cd flutter && git checkout stable
```

将如下环境变量添加到`~/.bashrc`, 然后重启shell.

```bash
# 使用中国节点加速下载
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
export PATH="$PATH:~/flutter/bin"
```

`flutter`命令的执行需要联网下载某些需要的程序, 国内网络下载很慢, 可以在运行这些命令之前, 设置一下代理.

```bash
export http_proxy=http://192.168.43.1:1080/
export https_proxy=http://192.168.43.1:1080/
```

当然, 前提是有一个可以工作的代理.

此代理设置仅仅对当前的shell环境生效, 退出当前shell后失效, 下一次打开一个新的shell时要重新设置.

预安装一些开发包:

```bash
flutter precache -v
```

禁止遥测:

```bash
flutter config --no-analytics
```

接受licenses:

```bash
flutter doctor --android-licenses
```

## 安装Chrome

从[google官网](https://www.google.com/chrome/)下载chrome, 选择`.deb`格式.

进入下载目录, 执行命令安装:

```bash
sudo dpkg -i google-chrome-stable_current_amd64.deb
sudo apt install -f
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

## 安装VS CODE的flutter插件

在VS CODE的插件管理页面搜索并安装`flutter`. [参考](https://flutter.dev/docs/get-started/editor?tab=vscode)

## Flutter SDK开启WEB支持

[参考](https://flutter.dev/docs/get-started/web#create-a-new-project-with-web-support)

```bash
flutter channel beta
flutter upgrade
flutter config --enable-web
```

执行命令检查设备:

```bash
flutter devices
```

输出应该有两条,一条为Chrome, 另一条为web server:

```bash
2 connected devices:

Web Server (web) • web-server • web-javascript • Flutter Tools
Chrome (web)     • chrome     • web-javascript • Google Chrome 85.0.4183.83
```

## 创建工程

参考[1](https://flutter.dev/docs/get-started/test-drive?tab=vscode)和[2](https://flutter.dev/docs/get-started/web#create-and-run)

### 在VS CODE里创建工程

`View` > `Command Palette`, 输入`Flutter: New Project`, 回车.

输入项目名称, 选择存放目录, 然后等待项目创建完成. 完成后, IDE会显示`main.dart`文件.

右下角的`Device`选择`Chrome`. 然后按F5键开始debug.

### 命令行创建工程

```bash
flutter create myapp
cd myapp
flutter run -d chrome
```

## 构建工程

执行

```bash
flutter build web
```

生成的文件位于`build/web`, 参考[部署文档](https://flutter.dev/docs/deployment/web).
