---
title: 在无序列表li前增加圆点标识符的几种方式
date: 2016-05-31 17:29:03
tags: [无序列表, HTML, CSS]
---
## 前言
在实际开发过程中，我们经常会用到`ul>li`标签，特别是对一系列样式相同且排列整齐的文档元素。
在默认情况下，我们都会给`ul`添加一个属性`list-style: none`，这样就能去除`li`标签开头的一个实心点。
因为去除`ul`样式的代码用习惯了(很多框架都重写了列表的默认样式)，以至于忘了`list-style`的存在。
<!-- more -->
## list-style
无序列表有一个CSS属性`list-style`，它其实是一个缩写，像`background`一样。
`list-style`包含了三个属性
```
ul {
    list-style-type: disc;
    list-style-position: outside;
    list-style-image: none;
}
/*Initial value*/
```
### list-style-type
在原始的无序列表中默认会有一个实心小圆点就是因为`list-style-type: disc`这个属性。
说到`list-style-type`，其下包含的类别有太多太多，有需要的同学可以点[这里](https://developer.mozilla.org/zh-CN/docs/Web/CSS/list-style-type)看看。
会用的其实不多，主要有`circle, square, upper-roman, lower-alpha`等。

### list-style-position
再说说`list-style-position`
> list-style-position: inside|outside|initial|inherit;

inside表示列表符包含在border以内，二outside则表示在border区域外。
<iframe width="100%" height="300" src="//jsfiddle.net/bill_q/o0L8pb69/embedded/html,css,result/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

### list-style-image
至于`list-style-image`就更简单了，用图片代替标识符。

## 几种增加圆点标识符的方式
### 默认样式
首先说的就是默认样式，如果不对ul进行任何CSS修饰，默认每个`li`标签前都会有一个小黑点标识符。
但是从视觉效果上看，这个标识符看上去并不是居中的，而是要稍偏下。
所以通常情况下很少直接这样拿来使用，给我们的设计稿如果有这效果需要实现，标识符一般都是居中的。
因此就要用到以下方法了。

### 使用before伪类
<iframe width="100%" height="100" src="//jsfiddle.net/bill_q/upfupf51/embedded/html,css,result/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
从上述代码中可以看出使用before添加伪类和默认样式的差别，很明显，默认样式的标识符明显下沉，而且使用伪类可以改变标识符的颜色。

### 添加新元素
要实现一个实心小圆的标识符，可以自己绘制或者添加图片来实现。
自己绘制的话我们使用一个空的`<i>`标签。
<iframe width="100%" height="200" src="//jsfiddle.net/bill_q/t0m7ka31/embedded/html,css,result/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

使用图片的道理也是一样的，通过`<img>`标签添加图片，在对其位置进行调整。但是使用图片存在一个问题，就是通常标识符的图片都比较小，会有图片失真的情况发生。

### list-style-image
通过这种方式添加图片标识符很方便，但是调整标识符图片的位置很不灵活，所以一般很少采用。

## 结语
有时候经验主义会很大地限制我们的思维，让我们一条路走到黑。在写代码的时候，多问问为什么，这样做虽然正确，但是不是最合适的？

End
