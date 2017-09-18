---
title: background八日谈第四日：background-size
date: 2016-05-13 15:04:50
tags: [CSS, background, background-size, ]
---
## 简介
`background-size`属性主要用来对`background-image`进行一系列的操作，功能很强大也非常有用。
```
html {
  background: url(greatimage.jpg);
  background-size: 300px 100px;
}
```

## 三种描述方式
- 单值
- 双值
- 多背景
<!-- more -->
### 单值(One Value)
单值的表示方式有两种：数值和关键字。
```
html {
    background: url(greatimage.jpg);
    background-size: 300px;
}
```
仅仅用一个数值描述`background-size`，如果容器的宽高足够，最后的得到的背景图片必然为一个矩形。因为背景图片的宽度值被定义为300px后，由于没有为其定高，则高度值默认为`auto`，也就是300px。
在用数值定义`background-size`时，可以使用任意合适的[CSS长度单位](https://css-tricks.com/the-lengths-of-css)。

同理也可以使用百分比来进行描述。
```
html {
    background: url(greatimage.jpg);
    background-size: 50%;   //50%图片的大小
}
```

再说说常用的两个关键词`cover`与`contain`。
- cover：把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。背景图像的某些部分也许无法显示在背景定位区域中。
- contain： 把图像图像扩展至最大尺寸，以使其宽度和高度完全适应内容区域。

### 双值(Two Values)
可以设置任意两个数值来描述背景图片的大小，第一个表示的宽，第二个表示高。如果数值设置过大，会导致图片失真。

### 多背景(Multiple Images)
CSS3的多背景是一个非常棒的属性，可以为一个元素设置多个背景图片，但是其中也有一些问题需要注意。
1. 背景图片层次结构。
<iframe width="100%" height="300" src="//jsfiddle.net/bill_q/jem3xuga/1/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
从上述代码中可以看出，背景图片有类似z-index的属性，最先声明的拥有最高的优先级，会把之后的声明的背景图片的重叠部分覆盖。
2. 单值、双值的方式可以混合使用，但注意要用逗号分隔。


TO BE CONTINUED!

参考文献：
[css-tricks](https://css-tricks.com)
