---
title: Linux 上 Git Proxy 设置
date: 2020-09-05 07:47:41
tags:
---

Git 代理的设置取决于要操作的仓库的协议类型.

## 使用 HTTP/HTTPS/FTP 协议的 repo

编辑`~/.bashrc`文件:

```bash
export http_proxy='http://myproxy.example.com:1080/'
export https_proxy='https://myproxy.example.com:1080/'
export ftp_proxy='http://myproxy.example.com:1080/'
export ALL_PROXY='socks://myproxy.example.com:1080/'
export all_proxy='socks://myproxy.example.com:1080/'
export no_proxy='example.com'
```

执行`source`生效:

```bash
source ~/.bashrc
```

## 使用 SSH 协议的 repo (git@)

安装 `socat`:

```bash
sudo apt update
sudo apt install socat
```

然后编辑`~/.ssh/config`:

```bash
Host Match *,!xxx.xxx.xxx.xxx
    ProxyCommand socat - PROXY:myproxy.example.com:%h:%p,proxyport=6004
```

config文件的语法: [Read more about SSH config files](https://linux.die.net/man/5/ssh_config)

## yocto 代理

安装 `socat`:

```bash
sudo apt update
sudo apt install socat
```

安装 `oe-git-proxy`

```bash
wget http://git.yoctoproject.org/cgit/cgit.cgi/poky/plain/scripts/oe-git-proxy
sudo mv oe-git-proxy /usr/local/bin/
sudo chmod +x /usr/local/bin/oe-git-proxy
```

编辑`~/.bashrc`文件:

```bash
export GIT_PROXY_COMMAND="oe-git-proxy"
export NO_PROXY=$no_proxy
```

执行`source`生效:

```bash
source ~/.bashrc
```

## 为某个仓库跳过代理

切换工作目录到要跳过代理的仓库, 然后执行:

```bash
git config --add remote.origin.proxy ""
```

`remote.origin`里的`origin`为远程仓库的名字, 如果想设置其他远程仓库, 将其替换为响应的名字, 例如`remote.upstream`

## 参考文档

[Working Behind a Network Proxy](https://wiki.yoctoproject.org/wiki/Working_Behind_a_Network_Proxy)
