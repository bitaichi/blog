---
title: SSH反向代理
date: 2020-09-05 21:27:39
tags:
---

## 环境配置

三台机器:

- A: 内网机器
- B: 外网服务器
- C: 第三台机器

期望达到的目标:

- 在 C 上面通过 B 访问 A

## 解决方案分析

A机器SSH连接到B, 同时建立从B到A的反向SSH隧道, 然后C通过B暴露出来的反向隧道端口来连接到A.

## 步骤

- 前提条件

  - A, B, C 三台机器均安装SSH客户端并支持用密钥登录
  - A, B 两台机器安装有SSH服务器

- 生成密钥对并安装到远端机器

  - 在A上生成密钥对, 并把公钥安装到B上;
  - 在C上生成密钥对, 并把公钥安装到A上.
  - 安全起见, A和B均配置为禁用密码登录.
  
- 配置中间服务器B

  B的防火墙需要开放SSH端口和一个额外的反代端口

  ```bash
  # use 2222 as the ssh port
  sudo ufw allow 2222
  
  # use the 3333 as the reverse ssh proxy port
  sudo ufw allow 3333
  ```

  修改`/etc/ssh/sshd_config` , 开启网关端口功能:

  ```bash
  # 若不开启此选项, 则只能在B上连接到A, 无法在C上经过B连接到A.
  GatewayPorts yes
  ```

  重启sshd服务.
  
- 建立B到A的反向隧道

  在A上安装`autossh`

  ```bash
  sudo apt update && sudo apt install autossh
  ```

  建立反代, 在A上执行:

  ```bash
  autossh -M 10984 -o "PubkeyAuthentication=yes" -o "PasswordAuthentication=no" -i /home/xxx/.ssh/id_rsa -R 3333:localhost:2221 root@IP_B -p 2222
  ```

  其中:
  - `-M`: 链路监听端口
  - `-i`: 私钥路径, 用来建立从A到B的连接
  - `-R`: `PORT_B:localhost:PORT_A`, 建立远端端口`PORT_B`到本地端口`PORT_A`的反向代理
         可以存在多个`-R`. 除了代理到localhost外, 也可以代理到局域网内的其他机器, 其他端口, 例如web服务器.
  
- C连接到A

  在C上执行:

  ```bash
  ssh USERNAME_A@IP_B -p 3333
  ```

## Systemd Service

example `/etc/systemd/system/autossh-jump-rtunnel.service`, replace Username and vps server IP and port with your value.

```bash
[Unit]
Description=AutoSSH reverse tunnel service
After=network-online.target ssh.service
StartLimitIntervalSec=0

[Service]
User=xxx
Environment="AUTOSSH_GATETIME=0"
ExecStart=/usr/bin/autossh -M 0 -o "ExitOnForwardFailure=yes" -o "ServerAliveInterval 30" -o "ServerAliveCountMax 3" -o "PubkeyAuthentication=yes" -o "PasswordAuthentication=no" -i /home/xxx/.ssh/id_rsa -NR 21000:localhost:22 root@xxx.xxx.xxx.xxx -p ssh_port
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

## Ref

- [SSH Port Forwarding Example](https://www.ssh.com/ssh/tunneling/example)
- [Persistent reverse (NAT bypassing) SSH tunnel access with autossh](https://raymii.org/s/tutorials/Autossh_persistent_tunnels.html)
- [SSH TUNNELLING FOR FUN AND PROFIT: AUTOSSH](https://www.everythingcli.org/ssh-tunnelling-for-fun-and-profit-autossh/)
- [使用SSH反向隧道进行内网穿透](http://arondight.me/2016/02/17/%E4%BD%BF%E7%94%A8SSH%E5%8F%8D%E5%90%91%E9%9A%A7%E9%81%93%E8%BF%9B%E8%A1%8C%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F/)
- [Welcome to the SSH Academy](https://www.ssh.com/ssh/ssh-academy)
