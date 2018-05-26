---
title: Vultr搭建SS
abbrlink: ef443e45
date: 2018-05-26 16:54:05
category: 开发工具
tags:
- Vultr
- Shadowsocks
- 科学上网
- 翻墙
---

嘘 ~ 安静，开始了。

<!-- more -->

## 注册 Vultr

进入 [Vultr](https://www.vultr.com/) 官网正常注册，然后通过邮箱激活账号。

这里特别说明一下，你可以上网上找一些优惠活动，比如下面这个：

[注册后充值 $10 送 $25 活动（不保证长期有效）](https://www.vultr.com/promo25b/?service=promo25b)

充值支持支付宝，所以购买还是很方便的，不过通过上面那活动注册的，想享受那个福利，必须用 paypal 充值才行。

## 购买服务器

首先选择地区，默认是日本东京。

![](https://i.loli.net/2018/05/26/5b097d1381ea2.png)

然后选择服务器的系统，这里就默认不用改动，选择 CentOS 7 x64 即可。

![](https://i.loli.net/2018/05/26/5b097e048ab00.png)

接着选择服务器规格，这里会发现日本东京 $2.5 每月的服务器已经售罄了，直选选择 $5 每月的了，如果想省钱的话，可以返回第一步，多切换不同的地区看看，有哪些地区还有 $2.5 每月的服务器。

![](https://i.loli.net/2018/05/26/5b097eca4965d.png)

{% note info %}
- Vultr 是按小时实时扣费的，并不是按月扣费的。
- 如果想创建多个服务器，需要提交申请，我的申请理由是：use to set up my server, like blog, bbs, etc.
{% endnote %}

最后点击“ Deploy Now ”创建服务器吧，创建好稍微等一会，等状态变成 Running 表示服务器已经启动好了。

![](https://i.loli.net/2018/05/26/5b09830d2c868.png)

## 连接服务器

这里我使用的是 [XShell](http://www.netsarang.com/products/xsh_overview.html) 连接服务器。

首先点开刚才创建好的服务器，看到这个界面：

![](https://i.loli.net/2018/05/26/5b09844e89dca.png)

{% note info %}
IP Address：服务器IP
Username：登录用户名
Password：登录密码
{% endnote %}

接下来打开 XShell ，点击“文件 - 新建”，依次配置好服务器信息。

![](https://i.loli.net/2018/05/27/5b0985f882f7a.png)

![](https://i.loli.net/2018/05/27/5b098652979e2.png)

创建好后并连接，看到如下界面，则表示连接成功了。

![](https://i.loli.net/2018/05/27/5b09870a28252.png)

## 安装SS

执行 [shadowsocks 一键安装脚本](https://teddysun.com/486.html)

```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

可以一路默认安装，当然建议是安装的时候就把密码、端口，以及加密方式选择好，省得之后还得修改。

安装成功后会看到如下提示：

```
Congratulations, your_shadowsocks_version install completed!
Your Server IP        :your_server_ip
Your Server Port      :your_server_port
Your Password         :your_password
Your Encryption Method:your_encryption_method

Your QR Code: (For Shadowsocks Windows, OSX, Android and iOS clients)
 ss://your_encryption_method:your_password@your_server_ip:your_server_port
Your QR Code has been saved as a PNG file path:
 your_path.png

Welcome to visit:https://teddysun.com/486.html
Enjoy it!
```

脚本默认创建是单用户的配置，如果要设置多用户，则需要到配置文件里修改配置，不同版本的配置文件路径也不同：

- Shadowsocks-Python 版：`/etc/shadowsocks-python/config.json`
- ShadowsocksR 版：`/etc/shadowsocks-r/config.json`
- Shadowsocks-Go 版：`/etc/shadowsocks-go/config.json`
- Shadowsocks-libev 版：`/etc/shadowsocks-libev/config.json`

{% note info %}
上面的配置文件路径保不准什么时候会变，就像网上大部分的教程都是说配置文件是 `/etc/shadowsocks.json` 这个路径一样。所以最简单的方法就是通过 `find / -name *.json` 查一下有哪些 json 文件。
{% endnote %}

多用户多端口配置格式参考如下：

```
{
    "server":"0.0.0.0",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
         "8989":"password0",
         "9001":"password1",
         "9002":"password2",
         "9003":"password3",
         "9004":"password4"
    },
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

修改完成后需要执行 `reboot` 重启服务器。

{% note info %}
执行对应命令进入配置文件后，按 `i` 键进入编辑模式，左下角出现 `-- INSERT --` 字样后，表示可以开始编辑。
编辑完成后按 `ESC` 键退出编辑模式，左下角 `-- INSERT --` 字样消失，输入 `:wq` ，回车执行，这时候文件会保存并返回。
{% endnote %}

## 安装 TCP 加速软件

因为服务器在国外，可能会不太稳定，这时候就很有必要安装 TCP 加速软件来提速，一般有锐速和 Google BBR 。

具体就不多介绍了，安装教程如下：

- [Vultr 专用破解版锐速一键安装脚本](https://www.vultrcn.com/7.html)（推荐）
- [原版 & 魔改版 Google BBR 拥塞控制算法一键安装脚本](https://www.vultrcn.com/5.html)

## 最后

最后下载对应的 [shadowsocks 客户端软件](https://www.shadowsocks.org/en/download/clients.html)，配置好，开始科学上网吧。

当然也可以配合我的上篇文章《{% post_link 网件R7000开启科学上网 %}》，直接在路由器里配置好，也能科学上网。

## 参考

- [实战vultr搭建SSR+锐速——超速看youtube1080p](https://www.qcgzxw.cn/1640.html)
- [Vultr VPS主机快速安装Shadowsocks（ss）完整图文教程](http://vultr.aicnm.com/Vultr-VPS%E4%B8%BB%E6%9C%BA%E5%BF%AB%E9%80%9F%E5%AE%89%E8%A3%85Shadowsocks%EF%BC%88ss%EF%BC%89%E5%AE%8C%E6%95%B4%E5%9B%BE%E6%96%87%E6%95%99%E7%A8%8B/)
- [搬瓦工shadowsocks多用户配置教程](http://calonye.com/22419.html)
- [Vultr 一键搭建酸酸 Shad0ws0cks 图文教程（推荐）](https://www.vultrcn.com/6.html)