---
title: Web APP 在线升级更新
tags:
  - webapp
  - hbuilder
category: WEB开发
abbrlink: d8a12da8
date: 2018-04-23 16:42:17
---

{% note info %}
本文提及的 webapp 指的是通过 HBuilder 打包出来的 APP
{% endnote %}

APP 离不开一个功能，那就是更新，一般我们会选择去应用市场里统一更新，但 APP 如果本身带有检查更新并升级的功能，那就再好不过了。接下来我就拿一个实际的例子介绍下，我是如何做更新功能的。

<!-- more -->

## 制作升级包

在 HBuilder 中生成升级包文件，这里需要注意，生成升级包前记得把版本号先修改

![](https://i.loli.net/2018/04/23/5adda1bfc8d6c.png)

选择好保存的路径，点确定即可，最终生成出来的升级包文件后缀是 .wgt 文件

![](https://i.loli.net/2018/04/23/5adda297ad08d.png)

然后我们需要将升级包放到一个通过网址可以访问到的地址，比如：http://www.demo.com/H55D8AE4A.wgt

## 检查更新

发起 ajax 请求检测是否有新版本

```javascript
$.ajax({
    url: '[这里替换连接地址，该连接地址返回版本号和升级包下载地址]'
}).done(function(cb){
    plus.runtime.getProperty(plus.runtime.appid, function(inf){
        if(cb.version != inf.version){
            downloadWgt(cb.url);
        }else{
            alert('已经是最新版本');
        }
    });
});
```

ajax 请求返回 json 数据如下

```json
{
    "version": 0.8,
    "url": "http://www.demo.com/H55D8AE4A.wgt"
}
```

## 下载升级包

从服务器上下载 wgt 升级包

```javascript
function downloadWgt(url){
    plus.nativeUI.showWaiting('下载中...');
    plus.downloader.createDownload(url, {filename: '_doc/update/'}, function(d, status){
        plus.nativeUI.closeWaiting();
        if(status == 200){
            installWgt(d.filename);
        }else{
            plus.nativeUI.alert('下载失败！');
        }
    }).start();
}
```

## 安装更新

```javascript
function installWgt(path){
    plus.nativeUI.showWaiting('安装中...');
    plus.runtime.install(path, {force: true}, function(){
        plus.nativeUI.closeWaiting();
        plus.nativeUI.alert('更新完成，即将重启应用！', function(){
            plus.runtime.restart();
        });
    }, function(e){
        plus.nativeUI.closeWaiting();
        plus.nativeUI.alert('安装失败[' + e.code + ']：' + e.message);
    });
}
```

## 总结

基本是照着官方提供的 demo 做的，不太一样的地方是，官方 demo 里的升级包下载地址是写死的，我修改成通过接口将版本号和下载地址一起返回回来。

其次还有一些更好的优化体验，比如静默安装，每次启动 APP 先检查一次是否有更新，如果有则后台静默开始下载安装，安装完成后无需做提示，用户下次启动 APP 就是最新版。

最后就是，应用资源更新是违反 apple 的 appstore 政策的，我没尝试去上架，如果无法上架，有个解决办法，就是通过接口动态开启检查更新的界面，审核的时候关闭，审核通过就开启，当然还是不建议这样操作。本文更适用于 Android 版本。

## 参考

- [App资源在线升级更新](http://ask.dcloud.net.cn/article/182)