---
title: Electron 初体验
category: WEB开发
tags:
  - electron
  - 无边框窗口
  - 窗口拖动
  - 主进程
  - 渲染进程
  - 进程通信
abbrlink: 20f3f334
date: 2018-08-20 11:15:45
---

![](https://i.loli.net/2018/08/14/5b724978c354e.png)

平时习惯将一些偶尔看到并且感兴趣的技术文章放到收藏夹里，目的是等有空的时候再仔细拜读或学习，但无奈抵不住自己的惰性，于是收藏夹越来越臃肿。近期在整理的时候就发现了好几篇关于 [Electron](https://electronjs.org/) 的文章，但我已经记不起当时为何收藏，大概每个前端开发工程师都有个做软件的梦想吧。

因为收藏了蛮多 Electron 的文章，直接清理掉感觉可惜，于是趁近期工作任务不是很重的前提下，对 Electron 进行了简单的入门，并尝试用 Electron 开发一套公司内部 OA 系统，这也是这篇文章的由来（因为开发过 Web APP 版的 OA ，所以桌面版的 OA 开发起来不算太费劲，业务代码基本可以沿用，主要的坑还是在 Electron 上）。

<!-- more -->

## 入门

快速入门我使用了官方的 [electron-quick-start](https://github.com/electron/electron-quick-start) 库，下载这个库后直接安装依赖，然后运行，就可以看到如下的效果。

```bash
# 安装依赖
npm install
# 运行
npm start
```

![](https://i.loli.net/2018/08/20/5b7a3b0440561.png)

## 主进程和渲染进程

主进程就是 package.json 里的 main 脚本，在当前这个库里，主进程就是 main.js 这个文件，在主进程中运行的脚本通过创建 web 页面来展示用户的界面。

通过上面的图我们可以了解到 Electron 的内核其实是 Chromium ，所以 Chromium 的多进程架构也会被使用到，那么每个 Electron 中的 web 页面都会运行在自己的渲染进程中。

简单的来说， main.js 就是主进程，在主进程里通过 BrowserWindow 创建页面，每个 BrowserWindow 就是渲染进程，当某个 BrowserWindow 页面被销毁后，对应的想染进程也会被终止。

## 优化窗口

因为 Windows 的窗口有够丑的，所以我的计划是不使用 Windows 的界面，用 HTML 和 CSS 自己做一个界面。

### 无边框窗口

第一步就是隐藏原生的窗口，打开 main.js 可以看到这段创建窗口的代码：

```javascript
function createWindow () {
    // Create the browser window.
    mainWindow = new BrowserWindow({width: 800, height: 600});
    // and load the index.html of the app.
    mainWindow.loadFile('index.html');
    // Emitted when the window is closed.
    mainWindow.on('closed', function () {
        // Dereference the window object, usually you would store windows
        // in an array if your app supports multi windows, this is the time
        // when you should delete the corresponding element.
        mainWindow = null;
    });
}
```

通过阅读官方 BrowserWindow 的 [API](https://electronjs.org/docs/api/frameless-window) 文档可以知道，只需要设置 `frame: false` 就可以创建无边框窗口，当然还可以设置禁止改变尺寸，这时候创建窗口的代码就变成这样了：

```javascript
// Create the browser window.
mainWindow = new BrowserWindow({width: 800, height: 600, frame: false, resizable: false});
```

运行后就会看到这样一个界面：

![](https://i.loli.net/2018/08/20/5b7a5c74a0dbf.png)

### 窗口拖动

现在窗口已经没有标题栏和菜单栏了，接着就会发现一个问题，就是窗口没办法拖动了。这不是什么大问题，因为官方也想到这问题了，所以在[无边框窗口文档](https://electronjs.org/docs/api/frameless-window)里给出了解决方案，就是给拖动区域增加 `-webkit-app-region: drag` 的 CSS 代码。

```html
<body style="-webkit-app-region: drag"></body>
```

需要注意的是，如果在拖动区域里有按钮等需要点击的元素，需要增加 `-webkit-app-region: no-drag` 设置，否则会无法点击。

### 手动实现窗口拖动

虽然官方已经提供了无边框窗口拖动的解决方案，但实际使用上会发现一些问题。比如我已经设置了窗口是无法改变尺寸的，但是在设置窗口拖动的区域内双击鼠标，窗口会最大化，再比如在拖动区域右键，会弹出系统级别的菜单选项，这些问题我查了相关资料也没找到屏蔽的办法，所以我就在考虑用 js 来手动实现窗口拖动的效果。

解决办法其实和网页上拖动元素一样，只是在获取坐标和设置坐标上，需要通过 electron 来获取窗口坐标和设置窗口坐标，实现代码如下：

```javascript
var currentWindow = require('electron').remote.getCurrentWindow();
$(document).on('mousedown', '.app-drag', function(e) {
    e = e.originalEvent || e;
    var $el = $(e.target);
    var canDrag = $el.closest('.app-no-drag').length <= 0;
    if (canDrag) {
        var winPos = currentWindow.getPosition();
        var dX = e.screenX - winPos[0];
        var dY = e.screenY - winPos[1];
        $(document).on('mousemove', function(e) {
            e = e.originalEvent || e;
            var _x = e.screenX - dX;
            var _y = e.screenY - dY;
            currentWindow.setPosition(_x, _y);
        });
        $(document).on('mouseup', function(e) {
            $(this).off('mousemove').off('mouseup');
        });
    }
});
```

这样只需要在需要给拖动区域设置 `.app-drag` ，非拖动区域设置 `.app-no-drag` 即可。

这里我是用 jQuery 来实现的，如果在页面上引入 jQuery 会报错：**Uncaught ReferenceError: $ is not defined**，解决办法如下：

```html
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
<script type="text/javascript">
    if(typeof module === 'object'){
        window.jQuery = window.$ = module.exports;
    }
</script>
```

## 进程间通信

因为安全限制， web 页面不能直接访问原生 GUI 资源，在 Electron 中也是一样，渲染进程如果想要进行原生的 GUI 操作，就比如和主进程进行通信，请求相应的操作。在 Electron 中提供了几种通信方式

### [ipcMain](https://electronjs.org/docs/api/ipc-main) 和 [ipcRenderer](https://electronjs.org/docs/api/ipc-renderer)

在渲染进程中使用 ipcRenderer 模块向主进程发送消息，主进程中使用 ipcMain 模块接收消息，进行操作，如果还需要反馈，则通知渲染进程，渲染进程根据接收的内容执行相应的操作：

```javascript
// 渲染进程中
const {ipcRenderer} = require('electron');
ipcRender.send('somemsg', data);
ipcRender.on('replaymsg', (evt, otherData) => {
    console.log(otherData);
});
// 主进程中
const {ipcMain} = require('electron');
ipcMain.on('somemsg', (evt, data) => {
    console.log(data);
    evt.sender.send('replymsg', otherData);
});
```

### [remote](https://electronjs.org/docs/api/remote)

remote 模块为渲染进程和主进程通信提供了一种简单方法，可以看到在上面手动实现拖动窗口的效果里，就用到了 remote 模块。

### [webContents](https://electronjs.org/docs/api/web-contents)

```javascript
// 主进程中
win.webContents.on('did-finish-load', () => {
    win.webContents.send('ping', 'whoooooooh!');
});
// 渲染进程中
require('electron').ipcRenderer.on('ping', (event, message) => {
    console.log(message);
});
```

### 渲染进程之间通信

如果对数据的实时性没有要求，只是渲染进程之间数据共享，[官方建议](https://electronjs.org/docs/faq#%E5%A6%82%E4%BD%95%E5%9C%A8%E4%B8%A4%E4%B8%AA%E7%BD%91%E9%A1%B5%E9%97%B4%E5%85%B1%E4%BA%AB%E6%95%B0%E6%8D%AE%EF%BC%9F)是使用浏览器中已经实现的 HTML5 API 。

但如果对数据实时性要求高，那就要结合上面的方法来实现：

```javascript
// 主进程中，将两个窗口的 id 分别发送到对方的渲染进程里
mainWindow.webContents.on('did-finish-load', () => {
    mainWindow.webContents.send('getChild', {
        childId: childWindow.id
    });
});
childWindow.webContents.on('did-finish-load', () => {
    childWindow.webContents.send('getMain', {
        mainId: mainWindow.id
    });
});
// 渲染进程 mainWindow，通过 id 得到 childWindow 并发送一条消息
ipcRenderer.on('getChild', (event, arg) => {
    remote.BrowserWindow.fromId(arg.childId).webContents.send('say', 'Hello World!');
});
// 渲染进程 childWindow，接收 mainWindow 的消息
ipcRenderer.on('say', (event, arg) => {
    console.log(arg); // 输出：Hello World!
});
// 同理 childWindow 也可以通过 id 给 mainWindow 发送消息
```

## 总结

不明白的地方优先查看[官方文档](https://electronjs.org/docs)，因为官方文档太完整了，虽然部分还是英文的，没有翻译完整，但并不影响阅读，因为官方文档有很多例子，看代码就能理解，也可以下载官方的 [electron-api-demos](https://github.com/electron/electron-api-demos) 库，整理了部分 API 的实例，当然这个项目本身也是一个很好的 Electron 演示。

## 参考

- [我眼中的 Electron](https://juejin.im/entry/58869b3f8d6d810058d46135)
- [使用 Electron 在两个进程 (主进程和渲染进程) 之间进行通讯](https://segmentfault.com/a/1190000011507250)