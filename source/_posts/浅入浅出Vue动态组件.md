---
title: 浅入浅出 Vue 动态组件
abbrlink: '46704839'
date: 2018-08-21 14:00:41
category: WEB开发
tags:
- Vue
- Vue动态组件
---

![](https://i.loli.net/2018/07/30/5b5e7a54ebe8d.png)

浅入浅出居然出第二篇了，之前整理了一篇《{% post_link 浅入浅出Vue组件 %}》，这次就再深入一点，学习一下动态组件。

<!-- more -->

## 什么是动态组件

简单的说，[动态组件](https://cn.vuejs.org/v2/guide/components.html#%E5%8A%A8%E6%80%81%E7%BB%84%E4%BB%B6)就是将几个组件放在一个挂载点下，然后根据父组件里某个变量来决定显示哪个，也可以都不显示。

## 语法

动态组件的核心就是挂载点，在挂载点会使用 `<component>` 标签，然后配合使用 `v-bind:is="组件名"` ，会自动去匹配组件名，有则显示，没有则不显示，也就是通过修改 `is` 指令的值，就可以改变挂载的组件。

```html
<!-- 组件会在 currentComponent 改变时改变 -->
<component v-bind:is="currentComponent"></component>
```

## 实例

说再多不如一个例子来得容易理解，根据介绍来看，最简单的场景就是 Tab 切换了，那我们先把基本界面搭出来。

<iframe height='300' scrolling='no' title='3.动态组件-界面' src='//codepen.io/hooray/embed/qyJJPw/?height=300&theme-id=0&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/qyJJPw/'>3.动态组件-界面</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

在上面的界面里， Tab 内容区实际上就是组件的挂载点，那我们就把代码替换成：

```html
<!-- :tab="currentTab" 就是传入组件里的数据 -->
<component v-bind:is="currentComponent" :tab="currentTab">
```

接着我们准备 2 个 Tab 内容区的组件，同时注册好这 2 个组件：

```html
<!-- 为了做区分，这 2 个组件分别是展示 string 和 array 的数据 -->
<script type="text/x-template" id="tab-content-string">
    <div class="content">
        {{tab.content}}
    </div>
</script>
<script type="text/x-template" id="tab-content-array">
    <div class="content">
        <ul>
            <li v-for="v in tab.content">{{v}}</li>
        </ul>
    </div>
</script>
```

```javascript
Vue.component('tab-content-string', {
    props: ['tab'],
    template: '#tab-content-string'
});
Vue.component('tab-content-array', {
    props: ['tab'],
    template: '#tab-content-array'
});
```

再接着我们初始化一下这个 Vue 实例：

```html
<div id="app">
    <div class="title">
        <a href="#" :class="{'active': currentTab == v}" v-for="v in tab" @click="currentTab = v">{{v.title}}</a>
    </div>
    <component v-bind:is="currentComponent" :tab="currentTab"></component>
</div>
```

```javascript
var app = new Vue({
    el: "#app",
    data: {
        tab: [
            {
                title: "Tab1",
                content: "abcabc"
            },
            {
                title: "Tab2",
                content: ["abc", "def", "ghi"]
            }
        ],
        currentTab: ''
    },
    computed: {
        currentComponent: function() {
            return (
                "tab-content-" +
                (typeof this.currentTab.content == "string" ? "string" : "array")
            );
        }
    },
    created: function() {
        this.currentTab = this.tab[0];
    }
});
```

可以看到，在 `data` 数据里我定义了一个 `currentTab` 字段，并且实例在创建好后，将 `tab[0]` 的数据赋值给 `currentTab` ，这样可以将第一个 Tab 标签高亮，并显示出内容。

```javascript
created: function() {
    this.currentTab = this.tab[0];
}
```

同时，在 Tab 标签上绑定一个点击事件，当点击触发后，将当前 Tab 标签的数据赋值给 `currentTab` ，这样在加载不同组件的时候，可以传入不同的数据。

```html
<a href="#" :class="{'active': currentTab == v}" v-for="v in tab" @click="currentTab = v">{{v.title}}</a>
```

最后就是处理 `v-bind:is="currentComponent"` ，在 `computed` 里定义了 `currentComponent()` 这个方法，根据判断 `currentTab.content` 的类型，返回不同的组件名，实现动态组件的加载。

```javascript
computed: {
    currentComponent: function() {
        return (
            "tab-content-" +
            (typeof this.currentTab.content == "string" ? "string" : "array")
        );
    }
}
```

来看下完整的效果：

<iframe height='300' scrolling='no' title='4.动态组件' src='//codepen.io/hooray/embed/gdOqPV/?height=300&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/gdOqPV/'>4.动态组件</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## [keep-alive](https://cn.vuejs.org/v2/guide/components-dynamic-async.html)

当我们在动态切换这些组件的时候，可能会希望保留之前组件的状态，而不是每次切换后都是全新的界面。

打个比方，假设我们某个 Tab 内容里，又包了一个子 Tab ，就像下面这个演示一样：

<iframe height='300' scrolling='no' title='5.动态组件-没用keep-alive' src='//codepen.io/hooray/embed/xaxmjL/?height=300&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/xaxmjL/'>5.动态组件-没用keep-alive</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

我们会看到，当切换到 Tab2 的时候，里面是一个子 Tab ，默认显示在 Tab2-1 标签上，当我们对子 Tab 切换到第二个或者第三个标签后，切回到外层的 Tab1 标签再切换回来，会发现 Tab2 里的子 Tab 又回到第一个 Tab2-1 标签上了，这时候就是使用 `keep-alive` 的时候了。

使用 `keep-alive` 可以说是尤其简单，只需要用 `<keep-alive>` 标签将动态组件包裹起来就可以。

```html
<keep-alive>
    <component v-bind:is="currentComponent"></component>
</keep-alive>
```

来看看效果如何：

<iframe height='300' scrolling='no' title='6.动态组件-使用keep-alive' src='//codepen.io/hooray/embed/qMBwgx/?height=300&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/qMBwgx/'>6.动态组件-使用keep-alive</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

没有问题，动态加载的组件被缓存了起来。

## 参考

- [Vue.js动态组件解析](https://www.jb51.net/article/92285.htm)