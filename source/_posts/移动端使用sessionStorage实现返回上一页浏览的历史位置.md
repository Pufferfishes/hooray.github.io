---
title: 移动端使用 sessionStorage 实现返回上一页浏览的历史位置
abbrlink: 7765b5eb
date: 2018-05-11 15:15:56
category: WEB开发
tags:
- sessionStorage
- 分页
---

移动端列表页一般会采用滚动加载的方式，也就是滚动到靠近页面底部，自从触发加载下一页数据，整个过程通过 ajax 实现。但这也就出现了一个问题，比如当用户滚动到第三页，点击列表某一项进入详情页，这时候再返回列表页，会发现列表也数据被重置回第一页了，用户又得继续滑动才能回到之前浏览的历史位置。

这样的用户体验肯定是很糟糕的，那是否有合适的解决方案呢？

<!-- more -->

## PC 端是如何解决的

碰到这个问题，我的第一反应是看看 PC 端是如何解决这问题的，然后发现 PC 端完美避开了这一问题， PC 端通过可以打开新页面这一特性，完全不会存在这一问题。

即使列表页不是通过打开新页面的方式，但 PC 端还有一个特性就是， PC 端的列表是分页的形式，也就是翻页的同时会刷新页面，页码会带在 URL 链接上，这样在返回的时候，可以精确的返回到指定的页面。

## 移动端要如何解决

PC 端的解决方案似乎无法移植到移动端，首先移动端没有打开新页面这功能（微信内置浏览器不支持）。其次，如果采用分页的方式，那用户体验又下降了，用户不仅需要手动去点击翻下一页，而且因为是跳转页面，用户也无法体验到 ajax 无缝刷新的特性。难道就没有合适的解决方案了么？

当然不是的！

通过查了些资料，并且手动写了个 demo ，发现通过使用 sessionStorage 可以完美解决这一问题。

## 实现思路

思路其实很简单，就是在用户每次加载页面数据的时候，将本次加载的列表 json 数据、当前页码保存到 sessionStorage 里，需要注意的是，列表 json 数据是需要累加的，假设用户滚动加载到第三页，则 sessionStorage 里要存放三页的列表数据。

然后在用户点击列表某一项的时候，将当前列表距离页面顶部的距离记录下来，也存放到 sessionStorage 里即可。当然也可以给列表页增加一个滚动监听的事件，做到实时监听位置。

当用户从其它页面返回到列表的时候，先判断 sessionStorage 里是否有数据，如果有，则直接将 sessionStorage 里的列表数据复原到页面上，当前页码也更新成 sessionStorage 里存放的页码，这样做的目的是实现用户滚动页面可以继续往后加载数据。最后通过 sessionStorage 里存放的页面位置，定位到之前的位置即可。

如果返回到列表页，没有 sessionStorage 数据，那就什么都不做，还是按原有功能实现即可。

{% note info %}
为什么不用 cookie 或者 localStorage 来实现？因为 sessionStorage 的特性最适合，关闭页面后自动失效。这样就不会出现用户下次打开列表页，还是显示之前的内容。
{% endnote %}

## 演示

![](https://i.loli.net/2018/05/11/5af5692d2b088.png)

![](https://wx1.sinaimg.cn/mw690/60c18c1cgy1fr8b11n6ycg20a00k0qv8.gif)

<script src='//gitee.com/hooray/mescroll-sessionStorage/widget_preview'></script>

&nbsp;

{% githubCard user:hooray repo:mescroll-sessionStorage width:100% %}

## 难点

实现思路清晰后，核心部分反而没有什么难点，倒是在配合 [MeScroll](http://www.mescroll.com) 使用上，发现了几个需要注意的地方。

{% note info %}
不同的滚动加载控件需要根据控件本身一些功能设置实现我们想达到的效果
{% endnote %}

### 设置当前页码

通过 `mescroll.setPageNum(num);` 可以设置当前 page.num 的值，但通过阅读源码发现：

```javascript
/*设置page.num的值*/
MeScroll.prototype.setPageNum = function(num) {
    this.optUp.page.num = num - 1;
}
```

设置的页面会减 1 ，所以在触发 sessionStorage 进行设置页码的时候，需要手动加 1 。

### 隐藏上拉加载状态

在 demo 中我是使用 `mescroll.endBySize(dataSize, totalSize, systime);` 进行隐藏上拉加载的状态，通过阅读源码发现：

```javascript
/*联网回调成功,结束下拉刷新和上拉加载
 * dataSize: 当前页的数据量(必传)
 * totalSize: 列表所有数据总数量(必传)
 * systime: 服务器时间 (可空)
 */
MeScroll.prototype.endBySize = function(dataSize, totalSize, systime) {
    var hasNext;
    if(this.optUp.use && totalSize != null) {
        var loadSize = (this.optUp.page.num - 1) * this.optUp.page.size + dataSize; //已加载的数据总数
        console.log(loadSize, totalSize);
        hasNext = loadSize < totalSize; //是否还有下一页
    }
    this.endSuccess(dataSize, hasNext, systime);
}
```

这个方法实际上是通过 `( 当前页码 - 1 ) * 每页数量 + 当前页数量` 计算出已加载数据总数，然后去对比列表数据总数，判断出是否还有下一页。

既然理解了这个方法，那只要在复原 sessionStorage 里的列表数据后，模拟触发一次即可。

但这个方法里第一个参数 dataSize 并不好模拟，因为 sessionStorage 里只存放了列表数据合集。我的做法是通过 `数据合集数量 % 每页数量` 取出余数，这个余数其实就是我最后一次加载的列表数量，但需要注意的是，这个余数可能是 0 ，余数是 0 则代表最后一次加载的列表数量和每页数量一样，所以如果余数是 0 的时候，每页数量就是最后一次加载的列表数量。

{% note info %}
每页数量就是 page.size 的值，默认是 10
{% endnote %}

## 参考

- [h5页面使用sessionStorage滚动到上次浏览器位置](https://www.cnblogs.com/gaofengming/p/6149407.html)
- [H5 本地存储localStorage的使用（返回前一页浏览的历史位置）](http://www.mamicode.com/info-detail-2235267.html)