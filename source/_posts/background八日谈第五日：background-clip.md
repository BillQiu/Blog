---
title: background八日谈第五日：background-clip
date: 2016-05-15 16:21:29
tags: [CSS3, background, background-clip]
---
## 简介
`background-clip`是CSS3中新加入属性，用来设置元素的背景（背景图片或颜色）是否延伸到边框下面。

```
.border {
    background-clip: border-box;   //默认值
}
```
### 值(Values)
`background-clip`一共有三个值：
1. border-box
默认值，背景延伸到边框外沿（但是在边框之下）。
2. padding-box
边框下面没有背景，即背景延伸到内边距外沿。
3. content-box
背景裁剪到内容区 (content-box) 外沿。
<!-- more -->

## 示例(Demo)
<iframe width="100%" height="300" src="//jsfiddle.net/bill_q/rm4tov8q/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
上述4个例子的HTML代码结构完全相同，为div设置的背景颜色为黄色，边框颜色为半透明的蓝色。
当`background-clip`的值为默认值`border-box`时，可以看到边框的颜色变成了灰色。这是因为div的背景颜色延伸到了边框外沿，背景色黄色和边框的半透明蓝色混合在一起显示为灰色。
当`background-clip`的值为`padding-box`时，div的背景色被限制在外边框之内，外边框的颜色为呈现为当初设置的半透明蓝。
当`background-clip`的值为`content-box`时，只有div中的内容——p标签——的背景色为黄色。div的padding和border没有背景色。这里需要注意一点，div容器内的p标签的整个盒模型(padding+marging+content)都会有黄色的背景色。

TO BE CONTINUED!

参考文献：
[css-tricks](https://css-tricks.com)
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-clip)
