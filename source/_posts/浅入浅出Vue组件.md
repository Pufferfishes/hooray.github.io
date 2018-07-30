---
title: 浅入浅出 Vue 组件
abbrlink: bb61bbd3
date: 2018-07-30 10:27:09
category: WEB开发
tags:
- Vue
- Vue组件
---

![](https://i.loli.net/2018/07/30/5b5e7a54ebe8d.png)

为什么标题要叫“浅入浅出”呢？因为是刚开始学 Vue.js（以下简称 Vue ），没有做很深入的研究，只是跟着官方[教程](https://cn.vuejs.org/v2/guide/)和 [API](https://cn.vuejs.org/v2/api/) 一点点熟悉语法，所以标题就叫了个“浅入浅出”，而这篇博文也更像一篇学习笔记。

在学习官方[教程](https://cn.vuejs.org/v2/guide/)的时候，我发现模板语法、计算属性、条件和列表渲染，还有事件处理，基本看一遍教程，上手熟悉下就基本都能掌握。唯独组件这块花了很多时间，并且在写一个 todolist 的 demo 时候踩了很多坑，又是看教程，又是查资料，才感觉初步掌握。

{% note info %}
这篇文章没有用到 Webpack 、Node.js 技术，也没有使用到 Vue 里的单文件组件，仅用最低成本方式，直接在页面里引用 Vue 的 CDN ，并通过 `Vue.component();` 方法直接创建组件。
{% endnote %}

<!-- more -->

## 一个待改造的实例

<iframe height='300' scrolling='no' title='1.非组件实例' src='//codepen.io/hooray/embed/djJbmr/?height=300&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/djJbmr/'>1.非组件实例</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

这是一个练习的实例，也是按照官方教程一步步走下来制作完成了，里面涉及了蛮多比如循环、条件判断、计算属性这些知识点，由于没有什么难度，实现过程就不说了，主要实现了以下几个功能：

- 基本 todolist 功能，可新增、删除 todolist 项
- 可对 todolist 项设置为完成（删除线），并可撤销
- 可对 todolist 项编辑（双击）
- 可统计当前 todolist 总数、已完成数、未完成数

下面就会在这个实例的基础上，把它改造成一个 todolist 的组件。

## 改造

### 组件注册

首先确认组件名，在 Vue 里可以采用 kebab-case（短横线分隔）或者 PascalCase（驼峰式）两种命名方式，区别在于组件名采用 PascalCase 命名时，自定义元素支持两种命名法，而组件名采用 kebab-case 命名时，自定义元素也必须使用 kebab-case 命名。

**kebab-case 命名**：

```javascript
Vue.component('my-component-name', {
    /* ... */
});
```

```html
<my-component-name></my-component-name>
```

**PascalCase 命名**：

```javascript
Vue.component('myComponentName', {
    /* ... */
});
```

```html
<myComponentName></myComponentName>
<!-- 或者 -->
<my-component-name></my-component-name>
```

这里我采用的是 kebab-case 的命名方式，取名叫 `todo-list` ，然后把实例里的部分选项，直接复制到组件注册的代码里，如下：

```javascript
Vue.component('todo-list', {
    // 注册局部指令
    directives: {
        select: {
            inserted: function(el){
                el.select();
            }
        }
    },
    computed: {
        undoLen: function(){
            var len = 0;
            this.lists.map(function(list){
                if(!list.isDone){
                    len += 1;
                }
            });
            return len;
        }
    },
    // 事件
    methods: {
        edit: function(index){
            if(!this.lists[index].isDone){
                this.lists[index].editText = this.lists[index].text;
                this.lists[index].isEdit = true;
            }
        },
        editInput: function(index){
            this.lists[index].text = this.lists[index].editText;
            this.lists[index].isEdit = false;
        },
        remove: function(index){
            this.lists.splice(index, 1);
        },
        toggleDone: function(index){
            this.lists[index].isDone = this.lists[index].isDone ? false : true;
        }
    }
});
```

{% note primary %}
因为组件是可复用的 Vue 实例，所以它们与 `new Vue()` 接收相同的选项，例如 `data` 、`computed` 、`watch` 、`methods` 以及生命周期钩子等。仅有的例外是像 `el` 这样根实例特有的选项。
{% endnote %}

### 组件模板

接下来就是要注册组件模板，首先确定演示 demo 中列表和底部的统计是要做成组件，而输入框和确认按钮还是实例里的。确认好组件部分后，首先在页面上增加一段 `<script type="text/x-template"></script>` ，然后把列表和底部统计的代码复制到里面，最后给这段 `script` 标签增加一个 ID：

```html
<script type="text/x-template" id="todolistTemp">
    <div>
        <ul v-bind:class="activeClass">
            <li v-for="(list, index) in lists" :key="index">
                <template v-if="!list.isEdit">
                    <span v-bind:class="{done: list.isDone}" @dblclick.prevent="edit(index)">{{list.text}}</span>
                    <a href="#" @click="toggleDone(index)" v-if="!list.isDone">完成</a>
                    <a href="#" @click="toggleDone(index)" v-else>撤回</a>
                    <a href="#" @click="remove(index)">删除</a>
                </template>
                <template v-else>
                    <input type="text" v-model="list.editText" @keyup.enter="editInput(index)" v-select>
                    <button @click="editInput(index)">修改</button>
                </template>
            </li>
        </ul>
        <div>当前一共有 {{lists.length}} 条 todo-list，{{undoLen}} 条未完成，{{lists.length - undoLen}} 条已完成</div>
    </div>
</script>
```

{% note primary %}
组件模板必须只有一个根元素，如果有多个则会报错：**Component template should contain exactly one root element.** 这也是为什么我在上面代码里，在列表和统计代码外增加一个 `div` 标签的原因。
{% endnote %}

模板创建好后，到组件注册代码里关联一下（其实可以把 HTML 代码直接写到 `template` 参数里，但这样就不方便维护了）：

```javascript
Vue.component('todo-list', {
    /* ... */
    template: '#todolistTemp',
    /* ... */
});
```

现在我的 todo-list 组件就基本注册创建好了，然后我在页面里就可以直接调用 `<todo-list></todo-list>` 就可以了，就像这样：

```html
<div id="app">
    <input type="text" v-model="text" @keyup.enter="add">
    <button @click="add">新增</button>
    <todo-list></todo-list>
</div>
```

万事俱备，那就来运行下看看，运行后发现页面上并看不到组件，并且代码报错了：

![](https://i.loli.net/2018/07/30/5b5ec758acf4c.png)

报错的信息大概意思是说 activeClass 和 lists 这两个属性或方法未定义，但我命名在实例里定义过这两个参数了啊，为什么组件里会提示这个错误呢？原因就是因为没有把数据传递到组件里。

### 通过 props 向组件传值

因为没有给组件传值，所以实例里定义的数据在组件里无法直接使用，需要用下面这个方法给组件传入数据，首先在组件调用的时候，可以设置传入的数据：

```html
<todo-list :lists="lists" :active-class="activeClass"></todo-list>
```

然后在组件注册里定义 props ：

```javascript
Vue.component('todo-list', {
    /* ... */
    props: ['lists', 'activeClass'],
    /* ... */
});
```

{% note primary %}
因为在 HTML 中的特性名是大小写不敏感的，所以浏览器会把所有大写字符解释为小写字符。所以在模板里使用时，要使用等价的 kebab-case 命名。
{% endnote %}

## 最终效果

最终运行成功，也没有报错了，效果如下：

<iframe height='300' scrolling='no' title='2.组件注册' src='//codepen.io/hooray/embed/rrpaKL/?height=300&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/rrpaKL/'>2.组件注册</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>