---
title: gitignore如何忽略之前已提交的文件
category: 开发工具
tags:
  - git
abbrlink: 7d8efe4e
date: 2018-04-20 14:38:49
---

做开发时，有些编译生成的代码我们希望不提交到 git 上，这时候就要使用 .gitignore 对文件/文件夹进行忽略，但有时候会碰到个棘手的问题，就是之前已经正常提交到 git 上的文件，即使在 .gitignore 进行了忽略，但代码仓库里依旧还是存在。

<!-- more -->

其实通过几行简单的命令就可以轻松解决这个问题

**首先删除本地缓存**

```
$ git rm -r --cached .
```

**新建/修改 .gitignore 文件**

将需要忽略的文件/文件夹路径写到 .gitignore 里

**commit 本次变更**

```
$ git commit -m "本次提交说明"
```

**推送到代码仓库**

```
$ git push
```