---
title: 响应式设计通过padding实现固定比例展示图片
abbrlink: 72a9e44f
date: 2018-07-13 10:08:05
category: WEB开发
tags:
  - 响应式设计
---

上个月写过一篇《{% post_link 响应式设计通过padding实现固定比例展示图片 %}》的文章，使用占位图解决了当图片尺寸不同情况下，如何在响应式设计下做统一展示。

今天看到有网友在那篇文章下面留言说，通过 `padding` 也可以解决这问题，其实之前前同事也在朋友圈和我说过，我突然意识到我可能错过了什么，于是连忙查了相关资料，发现确实是个不错的思路，所以这篇文章就记录一下这个解决方法。

<!-- more -->

## 解决思路

首先我们要了解一个知识点，就是**当任意方向的 `padding` 设为百分比时，都是相对于宽度计算的**。

明白了这点，我们就可以通过设置 `padding-top` 或者 `padding-bottom` 来实现一个我们想要的高宽比，举个例子，比如我们想要一个 1:1 的正方形：

<iframe height='400' scrolling='no' title='1.响应式设计通过padding实现固定比例展示图片' src='//codepen.io/hooray/embed/GBpKgN/?height=400&theme-id=0&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/GBpKgN/'>1.响应式设计通过padding实现固定比例展示图片</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

或者是高宽比为 1:2 的长方形：

<iframe height='300' scrolling='no' title='2.响应式设计通过padding实现固定比例展示图片' src='//codepen.io/hooray/embed/PBPowB/?height=300&theme-id=0&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/PBPowB/'>2.响应式设计通过padding实现固定比例展示图片</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

可以看出，上面两个案例里的 `padding-top` 刚好为**高/宽**的值，那么接下来要做的就简单了，既然容器的宽高比能够定死，那么容器里面的图片，只要拉伸到和容器一样的宽和高就可以了，就像这样：

<iframe height='300' scrolling='no' title='3.响应式设计通过padding实现固定比例展示图片' src='//codepen.io/hooray/embed/zLvYLz/?height=300&theme-id=0&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/zLvYLz/'>3.响应式设计通过padding实现固定比例展示图片</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

这里我将容器设为相对定位，图片设为决定定位，并且高度和宽度均为 100% ，这样图片和容器的高宽就一样了。

最后来看下在实际页面中的应用，这里我分别用三种尺寸的图片（200\*200、200\*220、180\*200）来模拟图片尺寸比例不同时，如何在响应式设计下实现统一比例的展示效果：

<iframe height='300' scrolling='no' title='4.响应式设计通过padding实现固定比例展示图片' src='//codepen.io/hooray/embed/YjXmXg/?height=300&theme-id=0&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/YjXmXg/'>4.响应式设计通过padding实现固定比例展示图片</a> by 代码小睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 总结

这个方法比起占位图的方法好处自然不用说，省去了制作/修改占位图的时间。但也有一定缺陷，就是这方法实际会强制拉伸图片为我们设置的高宽比，假设我们设置的高宽比是 1:2，但如果实际数据源提供的图片是正方形的话，强制拉伸后，图片就变形了。比起 `占位图` + `background-image:cover;` 的方法，对图片进行裁切，牺牲了部分内容的展现，保证了图片不会变形。

这两种方法各有取舍，看业务需求可以自由选择。

## 参考

- [CSS百分比padding实现比例固定图片自适应布局](https://www.zhangxinxu.com/wordpress/2017/08/css-percent-padding-image-layout/)