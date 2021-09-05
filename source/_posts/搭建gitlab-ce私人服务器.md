---
title: 搭建gitlab ce私人服务器
date: 2020-05-15 07:24:31
tags:
---

gitlab的搭建, 官方提供了很多不同的解决方案, 参见 [Install self-managed GitLab](https://about.gitlab.com/install/)

本文采用`docker`搭建方案.

<!--more-->

在撰写本文的时候, docker搭建支持两种方式, 一种是all-in-one的单一容器解决方案, 另外一种是多容器的解决方案.

因为此文的目的是搭建一个私人使用的gitlab, 故采用all-in-one的解决方案.

## 安装方式选择

官网参考文档给出了docker上三种不同的安装方式:

- 使用docker 引擎
- 使用docker-compose
- 使用docker swarm 模式

本文使用第二种方案, 即 docker-compose, 以便于后期的管理和升级.

## 搭建流程

开始之前确保`docker-ce`和`docker-compose`已经正确安装.

首先要准备一个比较大的分区, 然后在这个分区里创建一个文件夹作为gitlab的数据保存目录:

```bash
mkdir -p /mnt/data/52.gitlab-ce
```

本文里使用的目录为`/mnt/data/52.gitlab-ce`

下一步是创建docker-compose脚本的工作目录, 本文使用`~/workspace/docker/gitlab-ce`

```bash
mkdir -p ~/workspace/docker/gitlab-ce
cd ~/workspace/docker/gitlab-ce

touch docker-compose.yml
```

创建`docker-compose.yml`脚本, 内容为:

```yaml
gitlab:
  image: 'gitlab/gitlab-ce:latest'
  restart: always
  hostname: 'myprivategitlabce'
  environment:
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'http://192.168.100.105:6510'
      gitlab_rails['gitlab_shell_ssh_port'] = 6522
  ports:
    - '6510:6510'
    - '6522:22'
  volumes:
    - '/mnt/data/52.gitlab-ce/gitlab/config:/etc/gitlab'
    - '/mnt/data/52.gitlab-ce/gitlab/logs:/var/log/gitlab'
    - '/mnt/data/52.gitlab-ce/gitlab/data:/var/opt/gitlab'
```

配置详解:

- `image: 'gitlab/gitlab-ce:latest'` - 使用的镜像为官方的gitlab-ce
- `external_url 'http://192.168.100.105:6510'` - 外部访问地址和端口
- `gitlab_rails['gitlab_shell_ssh_port'] = 6522` - 外部SSH端口
- `'6510:6510'` - web端口映射
- `'6522:22'` - SSH端口映射
- `volumes:` - 文件夹映射

启动容器:

```bash
docker-compose up -d
```

容器的初始化要花一些时间, 观察容器状态:

```bash
docker ps -a
docker logs -f gitlab-ce_gitlab_1
```

等容器启动完毕, 登录`http://192.168.100.105:6510`按照提示创建root密码, 然后登录root账户

登录后, 需要进行一些配置, 以用于个人使用:

- 首先禁用用户注册: `http://192.168.100.105:6510/admin/application_settings/general#js-signup-settings`
- 然后, 添加一个个人用户, 使用此个人用户登录.

## 备份与升级

升级之前需要先备份

备份参见 [Back up GitLab](https://docs.gitlab.com/omnibus/docker/#back-up-gitlab)

升级参见 [update-gitlab-using-docker-compose](https://docs.gitlab.com/omnibus/docker/#update-gitlab-using-docker-compose)

## 参考文档

- [GitLab Docker images](https://docs.gitlab.com/omnibus/docker/)
