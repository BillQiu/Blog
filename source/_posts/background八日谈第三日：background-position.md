---
title: background八日谈第三日：background-position
date: 2016-05-09 11:45:52
tags: [CSS, background-position]
---
## 简介
首先从字面上理解background-positio——背景位置，那这里的背景指的是什么呐？
当然是[background-image](http://billqiu.com/2016/04/14/background%E5%85%AB%E6%97%A5%E8%B0%88%E7%AC%AC%E4%B8%80%E6%97%A5%EF%BC%9Abackground-image/)啦，对背景图片的位置进行调整。

## 兼容性
<iframe id="rewYxL" src="http://caniuse.com/css-background-offsets/embed" scrolling="no" frameborder="0" height="300" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe>
还是老样子，IE8以下不兼容。
<!-- more -->
## 三种描述方式
- 数值(e.g. 100px 5px)
- 百分比(e.g. 100% 5%)
- 关键词(e.g. top right)

### 数值
```
.lengthvalue {
    background-position: 10px 10px;
    //向右偏移10个像素，向下偏移10个像素。
}
```
如果不对background-position进行赋值，背景图片默认从右上角开始排列。
如果有2个描述，第一个表示水平方向上距离左边的偏移量，第二个表示垂直方向上距离上边的偏移量。(负值则相反)
不仅仅的是px，任何CSS的单位比如`em`、`rem`都可以用于其中。

### 百分比
```
.prcentages {
    background-position: 10% 10%;
}
```
百分比描述方式的偏移规则与用数值描述的相同。`10%`表示背景图片的中线与容器1/10处的分割线对齐。
最长的用`background-position: 50%`表示背景图片在容器的正中央(当只填写第一个数值时，第二个数值默认与第二个相同)。

### 关键词
一共有5个关键词`left`、`right`、`top`、`buttom`、`center`。
`left`：水平方向最左边
`right`：水平方向最右边
`top`：垂直方向最上边
`buttom`：垂直方向最下边
`center`：居中
这里有一个小tips `background-position: top center` = `background-position: center top`。当且仅当使用关键词描述时才有效。

## 声明数值的方式
我们可以使用1个数值来描述位置，也可以用两个。
甚至，可是使用三个或四个！
```
#threevalues {
  background-position: right 45px bottom;
  //表示距离右边45个像素，置底。
}
```
```
#fourvalues {
  background-position: right 45px bottom 20px;
  //表示距离右边45个像素，距离底边20个像素。
}
```
上述规则也很简单，先声明一个关键字，再进行百分比或数值进行具体描述。

## background-position-x/y
有时我们仅需要对背景图片的一个方向进行调整，便会用到`background-position-x`或`background-position-y`。
使用方法完全可以参照上文，但有一点需要注意，那就是兼容性。火狐浏览器对此属性的兼容性很差。
<iframe id="rewYxL" src="http://caniuse.com/background-position-x-y/embed" scrolling="no" frameborder="0" height="300" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe>

TO BE CONTINUED!

参考文献：
[css-tricks](https://css-tricks.com)
