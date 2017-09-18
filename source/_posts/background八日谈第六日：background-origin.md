---
title: background八日谈第六日：background-origin
date: 2016-05-17 10:35:08
tags: [CSS3, background, background-origin]
---
## 简介
在上文中我们介绍了`background-clip`属性，`background-origin`可是说是它的兄弟属性，也有三个值`border-box`、`padding-box`、`content-box`。
所以本文的主要目的就是介绍两者的区别。
<!-- more -->
## background-clip VS background-origin
首先，从两者的定义入手。
> background-clip : 设置元素的背景（背景图片或颜色）是否延伸到边框下面
> background-origin : 指定背景图片background-image 属性的原点位置的背景相对区域

从定义可以看出，当需要改变背景颜色(图片)的覆盖范围时，需要使用`background-clip`属性。
当需要改变背景图片(`background-image`)的起点位置时，需要使用`background-orgin`属性。

### 默认值
`background-origin`的默认值为`padding-box`
`background-clip`的默认值为`border-box`

想要加深对两者理解的可以用下面的例子把玩把玩。
<iframe width="100%" height="300" src="//jsfiddle.net/bill_q/xhsoy51v/1/embedded/html,css,result/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

TO BE CONTINUED!

参考文献：
[css-tricks](https://css-tricks.com)
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-origin)
