---
title: gulp-file-include 踩坑
category: 开发工具
tags:
  - Gulp
  - 自动化
abbrlink: 5f8cb75e
date: 2018-03-07 01:59:26
---

在之前的这篇 {% post_link 前端自动化工作流 [前端自动化工作流] %} 文章里，我主要提到了一个 gulp 插件，就是 [gulp-file-include](https://www.npmjs.com/package/gulp-file-include) 。在使用了一段时间后，也踩了不少坑，特此开一篇文章记录一下。

<!-- more -->

## 不支持 else 语法

查阅官方文档，发现是支持 if 语法的：

![](https://i.loli.net/2018/03/07/5a9eda9fd10a1.png)

在实际使用中，自然而然的就用上了 if/else：

```
@@include('test.html', {
    "age": 16
})
```

```
@@if(age >= 18){
    <b>允许进入</b>
}else{
    <b>禁止进入</b>
}
```

但输出的结果是：

![](https://i.loli.net/2018/03/07/5a9edf0c340a0.png)

所以这里只能写两个 if 来实现：

```
@@if(age >= 18){
    <b>允许进入</b>
}
@@if(age < 18){
    <b>禁止进入</b>
}
```

如果有更多条件判断，则继续增加 if 语句即可。

## 临时变量无法在 if/for/loop 嵌套中使用

这个描述可能不太好理解，具体看下这段代码：

```
@@for(var i = 0; i < arr.length; i++){
    @@if(i == 0){
        <b>这是第一条</b>
    }
}
```

我希望循环 arr 数组的时候，第一条输出“这是第一条”文字，但执行会提示 `i is not defined: (i == 0)` ，试了好多写法都不行，最后发现用反单引号（`{% raw %}`{% endraw %}`）加三元运算符可以解决这个问题：

```
@@for(var i = 0; i < arr.length; i++){
    `+(i == 0 ? '<b>这是第一条</b>' : '')+`
}
```