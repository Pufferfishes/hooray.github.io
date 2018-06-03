---
title: Atom 备份神器 —— Sync-settings
abbrlink: e7a857c2
date: 2018-06-03 23:54:53
category: 开发工具
tags:
- Atom
- 备份
- sync-settings
---

当 Atom 的扩展越装越多，配置也越来越偏离了系统默认设置时，一旦电脑重装后需要复原开发环境，这将是一件比较头疼的事。

今天就推荐一个扩展，可以解决这一问题，那就是 [Sync-settings](https://atom.io/packages/sync-settings) ，根据扩展介绍“ Synchronize settings, keymaps, user styles, init script, snippets and installed packages across Atom instances. ”，简言之就是可以同步 Atom 的设置文件、自定义快捷键、用户风格、初始化脚本及代码片段，还支持已安装的插件同步。

<!-- more -->

## 安装

在安装扩展里搜索“ sync-settings ”并安装，安装好后点击设置会看见下图：

![](https://i.loli.net/2018/06/04/5b1415c677142.png)

## 设置

第一步，打开自己的 github 创建一个 [personal access token](https://github.com/settings/tokens/new)（记住要勾选 gist 权限，见下图），然后复制生成的 token 序列粘贴到插件的上图中标 1 的红框里。

![](https://i.loli.net/2018/06/04/5b14160d26aaa.png)

第二步，打开 github 的 [gist](https://gist.github.com/) 服务，创建一个 gist，复制生成 gistID 粘贴到插件的上图中标 2 的红框里。

![](https://i.loli.net/2018/06/04/5b141639a1061.png)

![](https://i.loli.net/2018/06/04/5b14164447dd5.png)

## 备份与复原

以上就完成了所有配置，那如何进行备份呢？在文档编辑页面键入快捷键 `Ctrl` + `Shift` + `P` ，输入“ sync ”就能看到以下选项：

![](https://i.loli.net/2018/06/04/5b1416670ffd1.png)

选择第二个 Backup 就会开始进行备份了，备份好后，右上角会出现备份成功的提示：

![](https://i.loli.net/2018/06/04/5b14168178223.png)

复原的话，只需选择 Restore 就会自动进行复原，包括扩展也会同步安装，但有一点需要注意的是，扩展里的配置则不会备份，这个是需要手动去重新设置的，但即便如此，也已经节省了我们很大一部分时间了。

## 参考

- [Atom编辑器折腾记_(12)Sync-setttings(插件-备份神器)](https://blog.csdn.net/crper/article/details/47291063)