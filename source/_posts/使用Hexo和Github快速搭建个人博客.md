---
title: 使用 Hexo 和 Github 快速搭建个人博客
category: 工具推荐
tags:
  - Hexo
  - NexT
abbrlink: 15e2d21f
date: 2018-01-31 01:31:59
---

![](https://i.loli.net/2018/01/31/5a71327d1830b.png)

网上类似的教程一搜一大把，写得其实很详细。但对于初次接触的我来说，这是一次全新的体验。所以在经历了多次「安装——配置——部署——删除——再安装——」之后，大概弄懂了整个流程。正好也是博客的开篇文章，记录一下整个搭建流程，加深印象。

<!-- more -->

## 安装

### 安装前提

在安装前，先确保电脑上已经安装了 [Node.js](http://nodejs.org/) 和 [Git](https://git-scm.com/) 环境。

### 安装 [Hexo](https://hexo.io/zh-cn/)

``` bash
$ yarn global add hexo-cli
```

{% note info %}
官方是使用 npm 进行安装，个人推荐使用 [yarn](https://yarnpkg.com/zh-Hans/) 进行安装，关于 yarn 和 npm 优劣分析，可以参考这篇文章《[Yarn vs npm: 你需要知道的一切](http://web.jobbole.com/88459/)》
{% endnote %}

## 建站

### 初始化

先新建一个 `blog` 文件夹，然后进入该文件夹运行：

``` bash
$ hexo init
```

初始化完成后， `blog` 文件夹的目录如下：

```
.
├── .gitignore
├── _config.yml
├── package.json
├── yarn.lock
├── scaffolds
├── source
|   └── _posts
└── themes
```

### 本地访问

继续运行：

``` bash
$ hexo g && hexo s
```

运行成功后会发现提示信息：

> Hexo is running at {% raw %}http://localhost:4000/{% endraw %}.

这个时候访问 http://localhost:4000/ 就会发现网站已经建好了！

### 常用命令

以下 4 个是我在搭建过程中发现使用频率最高的 4 个命令，甚至我感觉只要会这 4 个命令就可以了。

**generate**

生成静态文件

``` bash
$ hexo generate
```

**server**

启动服务器

``` bash
$ hexo server
```

**deploy**

部署网站

``` bash
$ hexo deploy
```

**clean**

清除缓存文件（`db.json`）和已生成的静态文件（`public`）

``` bash
$ hexo clean
```

更多命令和参数请看 [这里](https://hexo.io/zh-cn/docs/commands.html) ！

### 部署到 Github

首先先到 Github 上新建一个仓库，仓库名的格式为：

```
<username>.github.io
```

例如我的就是 `hooray.github.io` ，创建好后，复制仓库的 HTTPS 地址，打开 `blog` 根目录下的 `_config.yml` 文件，拖动到底部找到 `deploy` 配置，按照以下格式修改并保存：

```
deploy:
  type: git
  repo: <仓库地址>
```

这个时候安装 [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git) 自动部署发布工具

``` bash
$ yarn add hexo-deployer-git
```

安装完成后，就可以去发布了

``` bash
$ hexo clean && hexo g && hexo d
```

第一次发布会提示输入 Github 账号和密码：

![](https://i.loli.net/2018/01/31/5a712b5a2e28b.png)

稍微等待一会，会提示发布成功，然后在浏览器里测试访问 [https://hooray.github.io](https://hooray.github.io) ，大功告成！

![](https://i.loli.net/2018/01/31/5a712cef6dc85.png)

这时候打开仓库会发现 Hexo 其实是把生成好的静态页面和相关资源上传到了 `master` 分支下，但是本地博客的开发环境是没有上传到仓库里的，如果换一台电脑想继续写博客更新，这就没办法。

解决这个问题其实也很简单，你可以单独再创建一个仓库，专门用于上传开发环境，但我的做法是创建一个分支，比如 `hexo` 分支，这个分支专门用来上传开发环境。

{% note warning %}
需要注意，通过 git clone 下来的 next 主题，需要手动删除 .git 文件夹，不然 NexT 主题不会被提交。
{% endnote %}

## 主题

### 安装 [NexT](http://theme-next.iissnan.com/)

``` bash
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```

{% note info %}
我安装的是 `6.0.x` 的 NexT ，但在 NexT 官网上提供的还是 `5.1.x` 的安装命令和文档，因为 `6.0.x` 和 `5.1.x` 的配置有所不同，建议第一次练习搭建还是使用 `5.1.x` ，熟悉配置文档后，再用 `6.0.x` 。
{% endnote %}

修改 `_config.yml` 文件里 `theme` 配置：

```
theme: next
```

### 配置

虽然我安装的是 `6.0.x` ，但大部分还是可以根据 NexT [主题配置](http://theme-next.iissnan.com/theme-settings.html) 文档来修改，以下会介绍一些文档中没有提及的设置。

{% note info %}
以下分别用：
- `站点配置` {% raw %}_config.yml{% endraw %}
- `主题配置` themes/next/{% raw %}_config.yml{% endraw %}

区分两个配置文件
{% endnote %}

#### 设置网站语言

打开 `站点配置` 找到 `language` 修改

```
language: zh-CN
```

#### 开启导航

打开 `主题配置` 找到 `menu` ，将需要开启的导航前面的 `#` 删掉

```
menu:
  home: / || home
  about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  commonweal: /404.html || heartbeat
```

这个时候会发现网站上已经能看到导航了，但点击却提示找不到页面。

这时需要去新建对应的页面，比如新建 `标签` 页：

``` bash
$ hexo new page "tags"
```

新建好后会在 `source/tags/index.md` 看到刚新建的文件，在 `Front-matter` 区域增加一句：

```
type: "tags"
```

其它导航页面操作一样。

#### 修改头像

打开 `主题配置` 找到 `avatar` 修改

```
avatar: /images/avatar.gif
```

#### 增加评论模块

在 NexT 官网上可以查到第三方评论系统的配置方法，我最终选择的是 [Gitment](https://github.com/imsun/gitment) 做为博客的评论系统，因为 Gitment 是一款基于 GitHub Issues 的评论系统，这样对于评论的管理完全可以在一个仓库里实现。

步骤如下：

首先点击 [这里](https://github.com/settings/applications/new) 注册一个新的 OAuth Application 。`Homepage URL` 和 `Authorization callback URL` 均填写博客地址就行

![](https://i.loli.net/2018/01/31/5a716e1494f32.png)

接着注册好后会得到 `Client ID` 和 `Client Secret` ，打开 `主题配置` 找到 `gitment` 对照着修改

```
gitment:
  enable: true
  mint: true
  count: true
  lazy: false
  cleanly: true
  language:
  github_user: hooray
  github_repo: hooray.github.io
  client_id: xxxxxxxxxx
  client_secret: xxxxxxxxxx
  redirect_protocol:
```

最后就是发布页面，发布好后，访问页面并使用你的 GitHub 账号登录（请确保账号是上面 repo 的拥有者），点击初始化按钮。之后其他用户就可以在该页面发表评论了。

{% note warning %}
需要注意的是，Gitment 不支持链接里面有中文，不然初始化评论的时候会提示 `Error: Validation Failed` 。解决办法就是创建文章的时候，使用纯英文当文件名。
{% endnote %}

#### 文章链接唯一化

上面说到创建文章的时候使用英文做文件名，但有有时候可能需要修改一篇已经发布的文章的标题，或者是修改它的发布时间，这样就导致文章链接地址会变化，也就间接导致 Gitment 评论的丢失。

解决这个问题需要安装 `hexo-abbrlink` 工具

``` bash
$ yarn add hexo-abbrlink
```

安装好后打开 `站点配置` 找到 `permalink` 修改

```
permalink: posts/:abbrlink/
```

并在增加如下代码

```
# abbrlink config
abbrlink:
  alg: crc32  # 算法：crc16(default) and crc32
  rep: hex    # 进制：dec(default) and hex
```

配置好后重新发布就能看到效果。

{% note primary %}
个人强烈推荐安装这个工具，这样创建文章的时候可以继续使用中文名，方便管理，同时也解决了上面的问题，并且对 SEO 友好。
{% endnote %}