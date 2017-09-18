---
title: 浅谈line-height与vertical-align对行内元素位置的影响
date: 2016-07-01 15:51:42
tags: [vertical-align, line-height, 行内元素, 位置]
---
## 前言

前几天在做项目的时候，突然遇到了一个文字下沉的问题。设法通过各种途径解决，在慕课网看了张老师的[课程](http://www.imooc.com/learn/542)后，终于恍然大悟，问题得以解决。
其实在看完课程后就想写篇博文来记录自己的学习收获，但是因为自己的拖延症一直拖到现在。庆幸的是当时做了比较详细的学习笔记，看看笔记还是能回忆起来一些。但是如果在学习完后能及时总结，收获肯定更大，能系统地描述自己的学习过程。
在此立个flag，以后学习完一个知识点后一定要及时总结回顾，一来能加深自己的理解，二来符合自己一贯推崇的“开源精神”。

<!-- more -->
## line-height
定义：两行文字基线之间的距离。

那么问题来了，什么是基线？
用过英文写字本的童鞋应该都记得其中有一条红色的线，那条就是所谓的基线。
不同的字体基线所在的位置是不同的。
微软雅黑的基线与x的底部重合。
![微软雅黑](/assets/blogImg/yahei.png)
而宋体和Arial字体则处于基线偏上的位置。
![Arial.png](/assets/blogImg/Arial.png)


应用
1、 使图片水平垂直居中
对父元素设置行高，注意是行高`line-height`而不是高度`height`，在子元素设置`vertical-align: middle`，图片就可实现垂直方向的居中。因为图片为`inline`水平的元素，所以为父元素设置`text-aline: center`即可实现水平居中。需要注意的一点是，此方法支持IE8及以上的浏览器。
<iframe height='265' scrolling='no' src='//codepen.io/bill_q/embed/wWqdaQ/?height=265&theme-id=dark&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='http://codepen.io/bill_q/pen/wWqdaQ/'>wWqdaQ</a> by bill (<a href='http://codepen.io/bill_q'>@bill_q</a>) on <a href='http://codepen.io'>CodePen</a>.
</iframe>

2、 使多行文本水平垂直居中
<iframe height='265' scrolling='no' src='//codepen.io/bill_q/embed/JKyWzK/?height=265&theme-id=dark&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='http://codepen.io/bill_q/pen/JKyWzK/'>JKyWzK</a> by bill (<a href='http://codepen.io/bill_q'>@bill_q</a>) on <a href='http://codepen.io'>CodePen</a>.
</iframe>

这里插一句，很多人为了让块级元素内的内容垂直居中，会吧高度和行高设置为相同的值。其实仅仅设置行高就可以达到想要的效果。


## vertical-align
在上面的使用`vertical-align: middle`居中的例子中，如果你仔细看一下，其实是可以看出来此居中并非完全居中，而是近似居中。为什么呐？这和`vertical-align: middle`实现居中的原理有关：先看看`middle`的定义：
> 元素中线与父元素的小写x中线对齐。

但是在前面我们就说到了，每种字体都是有偏移量的，会相对于中线下沉或上移，所以导致了并不是绝对居中。偏移量的大小又受到字体大小的影响，`font-size`越大，偏移量越大。
![move](/assets/blogImg/move.png)


下面是一个很经典的例子。
默认状态下，图片和文字(不包括xbase)的`vertical-align`值都为baseline。细心的同学可能已经观察到了，在图片的下方，有一条几个像素的边，也就是说图片没有填充满整个容器，那么这条边是哪里来的呐？
其实就是vertical-align和line-height‘搞基’产生的。
![line-height](/assets/blogImg/line-height.png)
如图一所示，图片的下边缘和小写字母x的下边缘是对齐的，也就是所谓的基线对齐。再对比图一和图二，图一中文字的`line-height`默认为`normal`，也就等于`height`为16px。图二的`line-height`为32px。这样就找出了问题所在的根源，在默认基线对齐的情况下，`line-height`影响了位置关系。
那么怎么样才能去除那条几个像素的边呐？
有两条思路：
1、 改变`vertical-align`对齐方式。
设置图片的`vertical-align`为middle、top、bottom；
在布局允许的情况下，可以改变图片的`display`水平为`block`，可以通过浮动、绝对定位等方法。
2、 修改`line-height`值。
将父元素的`line-height`设置为一个较小的值，直到边隙消失。
或者也可以改变父元素的`font-size`，因为`line-height`是根据`font-size`的大小决定的。

这里有一个现象需要解释下，当图片的`vertical-align`设置为`top`时，发现文字上移和图片顶部对齐了。这又是为什么呢？
其实原理是这样的，图片为了和整行文字的顶部对齐而下移了，但是由于DOM元素都是向左向上对齐的，所以产生了一个盒子内整体内容上移的现象，看上去好像是文字上移和图片去对齐。
在同一个包含盒子(containing box)内，如果使用`vertical-align`对行框盒子(line boxes)的位置进行调整，其相互之间的位置是互不影响的。

<iframe height='265' scrolling='no' src='//codepen.io/bill_q/embed/jAmYKg/?height=265&theme-id=dark&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='http://codepen.io/bill_q/pen/jAmYKg/'>jAmYKg</a> by bill (<a href='http://codepen.io/bill_q'>@bill_q</a>) on <a href='http://codepen.io'>CodePen</a>.
</iframe>

参考文献：
1. [慕课网](http://www.imooc.com/learn/542)
2. [MDN](https://developer.mozilla.org)
3. [张老师的博客](http://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/)
