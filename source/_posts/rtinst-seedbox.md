---
title: rtinst seedbox
date: 2020-09-05 21:32:54
tags:
---
rutorrent install scripts [rtinst](https://github.com/arakasi72/rtinst)

## setup VPS
<!--more-->
Install ubuntu 18.04

Install updates.

Create new user and add to sudoers group.

Modify the SSH port.

## setup rtinst

Install rtinst:

```bash
sudo bash -c "$(wget --no-check-certificate -qO - https://raw.githubusercontent.com/arakasi72/rtinst/master/rtsetup)"
```

Run rtinst:

```bash
sudo rtinst -d
```

If any error occurs, rerun the command.

If some packages cannot be installed, check the apt sources.list and add the `multiverse` repo.
