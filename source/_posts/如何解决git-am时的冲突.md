---
title: 如何解决`git am`时的冲突
date: 2020-09-05 07:44:02
tags:
---

首先暂停当前正在执行的`git am`:

```bash
git am --abort
```

然后启动一个新的3-way合并:

```bash
git am -3 < change.patch
```

接下来, 任选合适的方法来解决冲突:

- `git mergetool`
- or `vim` the conflict file ( to edit the standard <<<<<<, ||||||, >>>>>> conflict resolution)

解决完冲突之后, 重新`add`引发冲突的文件:

```bash
git add xxx.xxx
```

最后继续未完成的`git am`操作:

```bash
git am --continue
```
