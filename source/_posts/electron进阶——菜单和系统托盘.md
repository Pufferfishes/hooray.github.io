---
title: Electron进阶——菜单和系统托盘
category: WEB开发
tags:
  - electron
  - menu
  - menuitem
  - tray
  - 菜单
  - 系统托盘
abbrlink: 20beb467
date: 2018-08-30 13:19:12
---

![](https://i.loli.net/2018/08/14/5b724978c354e.png)

在《{% post_link electron初体验 %}》里我初步入门并上手了 Electron ，在这篇文章里，我会进一步熟悉 Electron 中菜单以及系统托盘的 API ，并实现对应的功能。

<!-- more -->

## [菜单](https://electronjs.org/docs/api/menu)

有两种菜单类型：原生应用菜单和上下文菜单

### 原生应用菜单

原生应用菜单可以理解为应用窗口菜单，当我们成功运行官方 [electron-quick-start](https://github.com/electron/electron-quick-start) 库的时候，下图红框区域就是原生应用菜单，因为我们没有自定义设置菜单，所以 Electron 会展示一个默认的菜单。

![](https://i.loli.net/2018/08/30/5b87555268b98.png)

### 上下文菜单

上下文菜单可以理解为就是页面上的菜单，最常见的应用就是鼠标右键菜单，当然触发条件不一定是鼠标右键，也可以是左键或者键盘事件。

## [菜单项](https://electronjs.org/docs/api/menu-item)

不管是原生应用菜单，还是上下文菜单，都需要配置菜单项， Electron 提供了 5 种类型的菜单项，分别是：

- normal （常规菜单项）
- separator （分割线）
- submenu （子菜单）
- checkbox （复选菜单项）
- radio （单选菜单项）

下面我用一个实际的例子来演示这 5 种类型的菜单项。

### 原生应用菜单演示

```javascript
// 主进程
const {Menu} = require('electron');

const menu = Menu.buildFromTemplate([
    {
        label: '原生应用菜单演示',
        submenu: [
            {
                label: '个人信息',
            },
            {
                type: 'separator'
            },
            {
                label: '开机启动',
                type: 'checkbox',
                checked: true
            },
            {
                type: 'separator'
            },
            {
                label: '性别',
                submenu: [
                    {
                        label: '男',
                        type: 'radio'
                    },
                    {
                        label: '女',
                        type: 'radio'
                    },
                ]
            }
        ]
    }
]);
Menu.setApplicationMenu(menu);
```

![](https://i.loli.net/2018/08/30/5b87664e0dd32.png)

可以看到， type 默认是 normal ，所以可以忽略，并且如果设置了 submenu 参数， type 也可以忽略。

### 上下文菜单演示

```javascript
// 渲染进程
const {remote} = require('electron');
const {Menu} = remote;

const menu = Menu.buildFromTemplate([
    {
        label: '个人信息',
    },
    {
        type: 'separator'
    },
    {
        label: '开机启动',
        type: 'checkbox',
        checked: true
    },
    {
        type: 'separator'
    },
    {
        label: '性别',
        submenu: [
            {
                label: '男',
                type: 'radio'
            },
            {
                label: '女',
                type: 'radio'
            },
        ]
    }
]);
window.addEventListener('contextmenu', function(e){
    menu.popup({
        window: remote.getCurrentWindow()
    });
}, false);
```

![](https://i.loli.net/2018/08/30/5b8764a89f2d9.png)

我们也可以换一种写法，通过 `menu.append()` 追加菜单项，比如下面这段代码，就实现了动态增加菜单项的功能。

```javascript
const {remote} = require('electron');
const {Menu, MenuItem} = remote;

const menu = new Menu();
menu.append(new MenuItem({
    label: '点我会增加一个菜单项',
    click: function(){
        menu.append(new MenuItem({
            label: '测试项'
        }));
    }
}));

window.addEventListener('contextmenu', function(e){
    menu.popup({
        window: remote.getCurrentWindow()
    });
}, false);
```

## [系统托盘](https://electronjs.org/docs/api/tray)

系统托盘就是桌面右下角那块常驻图标栏，系统托盘实际上也是一个菜单，通过点击鼠标右键触发。通过系统托盘可以给我们的应用实现一些有趣的特性，比如最小化到托盘，也就是关闭窗口但不退出应用。

下面我们就创建一个系统托盘，注意，系统托盘创建的时候，必须指定一个图标路径，因为在系统托盘区域，都是用图标展示的。

```javascript
// 主进程
const {app, Menu, Tray} = require('electron');

let tray = new Tray('build/icon.ico');
const contextMenu = Menu.buildFromTemplate([
    {
        label: '退出',
        click: function(){
            app.quit();
        }
    }
]);
tray.setToolTip('应用标题');
tray.setContextMenu(contextMenu);
```

这时候我们运行一下，发现没问题，达到我们想要的要求了，并且点击退出后，整个应用都退出了。

![](https://i.loli.net/2018/08/30/5b878a9853883.png)