---
title: CSS3 Flex布局入门
abbrlink: 6d2f7931
date: 2018-07-02 20:52:45
category: WEB开发
tags:
- flex
- flex布局
- css3
---

说来惭愧，对于 Flex 的了解都是碰到需求时，然后临时百度找现成的，一直没系统的学习过 Flex ，趁周末没什么事，抽了几小时学习并整理了这篇入门文档，方便自己以后快速查阅。

<!-- more -->

{% note warning %}
考虑移动端分辨率的情况，本文提供 CodePen 的演示，建议在 PC 端进行查看。
{% endnote %}

## 基本概念

Flex 全称 Flexible Box ，意思就是“灵活的盒子”，也就是给盒模型提供最大的灵活性，我们也可以称它为“弹性布局”。

我们可以给任何一个容器指定为 Flex 布局，就像这样：

```css
.box{
    display: flex;
}
/* 行内元素也可以使用 Flex 布局 */
.box{
    display: inline-flex;
}
```

{% note info %}
设为 Flex 布局以后，子元素的 `float` 、 `clear` 和 `vertical-align` 属性将失效。
{% endnote %}

我们一般把采用 Flex 布局的元素称为 Flex 容器（Flex container），它的所有子元素成为 Flex 项目（Flex item）。容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做 main start ，结束位置叫做 main end ；交叉轴的开始位置叫做 cross start ，结束位置叫做 cross end 。项目默认沿主轴排列，单个项目占据的主轴空间叫做 main size ，占据的交叉轴空间叫做 cross size 。

上面一大堆基本概念描述的比较晦涩难懂，不如下面一张图来得清晰明了。

![](https://i.loli.net/2018/07/02/5b3a1953d6df1.png)

## 容器的属性

容器一共有 6 个属性可以设置。

### 1. flex-direction

flex-direction 属性决定主轴的方向，也就是项目的排列方向，有 4 个可选值：

- **row**（默认值）：主轴为水平方向，开始位置在左边（从左到右）
- **row-reverse**：主轴为水平方向，开始位置在右边（从右到左）
- **column**：主轴为垂直方向，开始位置在上边（从上到下）
- **column-reverse**：主轴为垂直方向，开始位置在下边（从下到上）

具体可参考下面的演示：

<iframe height='352' scrolling='no' title='flex-direction' src='//codepen.io/hooray/embed/XYOwjw/?height=352&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/XYOwjw/'>flex-direction</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. flex-wrap

flex-wrap 属性决定是否换行，或者如何换行，有 3 个可选值：

- **nowrap**（默认值）：不换行
- **wrap**：换行，第一行在最上方
- **wrap-reverse**：换行，第一行在最下方

具体可参考下面的演示：

<iframe height='352' scrolling='no' title='flex-wrap' src='//codepen.io/hooray/embed/oyVvNg/?height=352&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/oyVvNg/'>flex-wrap</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. align-items

align-items 属性决定项目在交叉轴上如何对齐，有 5 个可选值：

- **flex-start**：交叉轴的起点对齐
- **flex-end**：交叉轴的终点对齐
- **center**：交叉轴的中点对齐
- **baseline**：基准线对齐
- **stretch**（默认值）：如果项目未设置高度或设为 auto ，将撑满整个容器的高度

具体可参考下面的演示：

<iframe height='422' scrolling='no' title='align-items' src='//codepen.io/hooray/embed/xzBWGO/?height=422&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/xzBWGO/'>align-items</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 4. justify-content

justify-content 属性决定项目在主轴上的对齐方式，有 5 个可选值：

- **flex-start**（默认值）：左对齐
- **flex-end**：右对齐
- **center**：居中
- **space-between**：两端对齐，项目之间间隔相等
- **space-around**：项目两侧间隔相等，也就是项目之间间隔比项目与两侧间隔大一倍

具体可参考下面的演示：

<iframe height='352' scrolling='no' title='justify-content' src='//codepen.io/hooray/embed/jKJWqV/?height=352&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/jKJWqV/'>justify-content</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 5. align-content

align-content 属性决定了当有多根轴线时如何对齐（如果只有一根轴线则不起作用），有 6 个可选值：

- **flex-start**：与交叉轴的起点对齐
- **flex-end**：与交叉轴的终点对齐
- **center**：与交叉轴的中点对齐
- **space-between**：与交叉轴两端对齐，轴线之间的间隔平均分布
- **space-around**：每根轴线两侧间隔都相等，也就是轴线之间间隔比轴线与两侧间隔大一倍
- **stretch**（默认值）：轴线占满整个交叉轴

具体可参考下面的演示：

<iframe height='570' scrolling='no' title='align-content' src='//codepen.io/hooray/embed/rKRdJL/?height=570&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/rKRdJL/'>align-content</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 6. flex-flow

flex-flow 属性是 flex-direction 属性和 flex-wrap 属性的简写形式，默认值分是 `row nowrap` ，语法如下：

```css
.box{
    flex-flow: <flex-direction> || <flex-wrap>;
}
```

## 项目的属性

项目也有 6 个属性可以设置。

### 1. align-self

align-self 属性可单独设置某个项目的对齐方式，有 6 个可选值：

- **auto**（默认值）：继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch
- **flex-start**：交叉轴的起点对齐
- **flex-end**：交叉轴的终点对齐
- **center**：交叉轴的中点对齐
- **baseline**：基准线对齐
- **stretch**：如果项目未设置高度或设为 auto ，将撑满整个容器的高度

具体可参考下面的演示：

<iframe height='422' scrolling='no' title='align-self' src='//codepen.io/hooray/embed/WymJvQ/?height=422&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/WymJvQ/'>align-self</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 2. flex-grow

flex-grow 属性定义了项目的放大比例，默认为 0 （负值对该属性无效），如果存在剩余空间，也不放大。

具体可参考下面的演示：

<iframe height='365' scrolling='no' title='flex-grow' src='//codepen.io/hooray/embed/BVbxrj/?height=365&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/BVbxrj/'>flex-grow</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 3. flex-shrink

flex-shrink 属性定义了项目的缩小比例，默认为 1 （负值对该属性无效），如果空间不足，该项目将缩小。

具体可参考下面的演示：

<iframe height='365' scrolling='no' title='flex-shrink' src='//codepen.io/hooray/embed/WymKbL/?height=365&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/WymKbL/'>flex-shrink</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 4. flex-basis

flex-basis 属性定义了在分配多余空间之前，项目占据主轴空间（main size）的大小。浏览器根据这个属性，计算主轴是否有多余空间。默认为 auto ，即项目的本来大小。

具体可参考下面的演示：

<iframe height='365' scrolling='no' title='flex-basis' src='//codepen.io/hooray/embed/VdRBzy/?height=365&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/VdRBzy/'>flex-basis</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 5. order

order 属性定义项目的排列顺序，数值越小，排越靠前，默认为 0 。

具体可参考下面的演示：

<iframe height='365' scrolling='no' title='order' src='//codepen.io/hooray/embed/LraBrO/?height=365&theme-id=0&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/hooray/pen/LraBrO/'>order</a> by 胡睿 (<a href='https://codepen.io/hooray'>@hooray</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 6. flex

flex属性是 flex-grow 属性， flex-shrink 属性和 flex-basis 属性的简写，默认为 `0 1 auto` ，后两个属性可选，语法如下：

```css
.item{
    flex: none | auto | [ <flex-grow> <flex-shrink>? || <flex-basis> ];
}
```

## 兼容性

在 [Can I Use](https://caniuse.com/) 网站可以准确的查看到 Flex 的兼容性，如下图：

![](https://i.loli.net/2018/07/03/5b3b13e5061c8.png)

可以看出各大浏览器对 Flex 的支持比较全面，是个可以放心使用的属性。

## 参考

- [CSS Flexbox – An Exploration](https://www.toobler.com/css-flexbox-an-exploration/)
- [CSS3 Flex布局（伸缩布局盒模型）学习](https://www.cnblogs.com/sxz2008/p/6635196.html)