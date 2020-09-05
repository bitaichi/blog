---
title: Use apt-cacher-ng to accelerate apt download
date: 2020-09-05 06:32:35
tags:
---

[About apt-cacher-ng](https://www.unix-ag.uni-kl.de/~bloch/acng/)

If you just want to use the current running apt-cacher-ng server, please just refer to the [Use on client side](#use-on-client-side)

## Setup apt-cacher-ng server

We use the docker container to run the apt-cacher-ng service, so first need to install docker, please refer to the docker installation guide of your Linux distro.

Then pull below image:

```bash
docker pull sameersbn/apt-cacher-ng:3.1-3
```

Please refer to the [docker hub page](https://hub.docker.com/r/sameersbn/apt-cacher-ng) for more information.

Next run the docker container:

```bash
docker run --name apt-cacher-ng --init -d --restart=always   --publish 3142:3142   --volume /mnt/data/51.apt-cacher-ng/cache-data:/var/cache/apt-cacher-ng   sameersbn/apt-cacher-ng:3.1-3 -- Proxy=http://192.168.100.105:6002
```

Some key parameters:

* `--publish 3142:3142`: This is the port mapping, here we use the default port, please change it if the default port has been occupied.
* `--volume /mnt/data/51.apt-cacher-ng/cache-data:/var/cache/apt-cacher-ng`: This is the mount point of the cache data, please use a folder that bigger enough.
* `-- Proxy=http://192.168.100.105:6002`: This is the so-called 'upstream' proxy, i.e. the proxy for connecting to the outside Internet.

Use `docker ps -a` to check the container status, make sure no errors happen.

## Use on client side

First we need to add the server ip to the `no_proxy` system environment:

```bash
export no_proxy=$no_proxy,192.168.100.105
```

To make persistent, add into the `~/.bashrc` or `~/.profile`. Do not forget to `source` the file or logout-then-login to take effect.

Also we may need to modify the `~/.docker/config.json` to add the server ip into the `no_proxy` env. It depends.

Then change the apt source file, typically `/etc/apt/sources.list`, search for `http://` or `https://`, replaced with `http://<your-server-ip>:3142/`

Then just run the apt commands as usual.
