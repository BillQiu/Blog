---
title: background八日谈第一日：background-image
date: 2016-04-14 13:47:18
tags: [background, background-image, CSS, linear-gradient]
---
## 开宗明义
`background`，一个最常用的CSS属性，只要是写过CSS的人，都肯定用过这个属性。
正如他的字面意思：背景，我们平常更多地用他来设置背景颜色。殊不知background是一个强大的家族，共有八个兄弟姐妹，background八日谈系类文章将分八个章节对他们进行一一介绍，相信你定会对他们有一个新的认识。
```
body {
  background:
     url(sweettexture.jpg)    /* image */
     top center / 200px 200px /* position / size */
     no-repeat                /* repeat */
     fixed                    /* attachment */
     padding-box              /* origin */
     content-box              /* clip */
     red;                     /* color */
}
```
<!-- more -->
## 图片类型
在CSS中，图片大致大致可以分为两大类：常规图片(regular images)和渐变图片(gradients)，本文也根据图片类型分为两部分来介绍`background-image`。

### 常规图片(regular images)
从我们最常用的常规图片说起，`background: url(sweettexture.jpg);`这个属性大家都不会陌生，对相应的元素设置一个名为sweettexture的jpg格式的是图片。
当然这只是最基础的，如果我们想让图片置于一个合理的位置，常常会与`background-repeat`、`background-position`等属性配合使用。
在设置常规图片时，一定要考虑到浏览器是否能识别图片的格式，几乎所有的浏览器都支持JPEG、GIF、PNG格式的图片，如果要用到其他格式的图片就要慎重考虑了。至于以上三种图片的应用场景，以超出本文的讨论范围，请大家自行[google](https://www.google.com)(不要百度！不要百度！不要百度！)

除了以上几种常用的引入图片的类型，还有一种base64的图片格式，大家可能用的比较少。
```
background-image:url(data:image/gif;base64,R0lGODlhBAABAIABAMLBwfLx8SH5BAEAAAEALAAAAAAEAAEAAAICRF4AOw==);
```
`data:[<mime type>][;charset=<charset>][;base64],<encoded data>`
优点：
- 减少了HTTP请求
- 某些文件可以避免跨域的问题
- 没有图片更新要重新上传，还要清理缓存的问题
缺点：
- 增加了CSS文件的尺寸
- 浏览器支持
想要了解base64图片的，可以看看张老师写的这篇[文章](http://www.zhangxinxu.com/wordpress/2012/04/base64-url-image-%E5%9B%BE%E7%89%87-%E9%A1%B5%E9%9D%A2%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96/)。

对同一个元素，我们可以通过`background-image`设置多个背景图片。
```
body {
  background: url(logo.png), url(background-pattern.png);
}
```
这里有一点需要注意——图片的设置顺序，越先声明的层次优先级越高。
举个栗子，如果声明的第二个背景图片的尺寸比第一个小，并且没有进行定位，就会被第一个申明的背景图片覆盖。
<iframe width="100%" height="200" src="//jsfiddle.net/bill_q/34pyvr7k/1/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

在使用背景图片的时候，我们有时会遇到图片无法加载的情况，为此可以为背景图片设置一个<b>回调颜色</b>，在图片无法加载的情况下代替图片显示。
<iframe width="100%" height="150" src="//jsfiddle.net/bill_q/70x9cbLo/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

### 渐变图片(gradients)
<p style="font-size: 17px">线性渐变</p>
```
background: linear-gradient(angle, color-stop1, color-stop2);
```
依旧从我们最熟悉的线性渐变开始说起，如果没有设置angle值，默认从上到下。
<iframe width="100%" height="200" src="//jsfiddle.net/bill_q/sy1Laak7/1/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

使用to表示颜色的延生方向，一共有八个方向
to `top/bottom/right/left/top left/top right/buttom left/buttom right`
<iframe width="100%" height="200" src="//jsfiddle.net/bill_q/xw4251ov/2/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

如果想要更加精确的颜色延生角度，可以使用`deg`
标记12点方向为0度，顺时针方向增加，90deg = to right,-90deg = to left
<iframe width="100%" height="200" src="//jsfiddle.net/bill_q/cf7zcu55/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

当想要控制色块中某种颜色开始或结束的位置，可以使用`%`进行控制
<iframe width="100%" height="200" src="//jsfiddle.net/bill_q/73m47n7L/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

<p style="font-size: 17px">径向渐变</p>
```
background: radial-gradient(shape size at position, start-color, ..., last-color);
```
径向渐变与线性渐变大体相同，主要区别在于angle与shape size at position上。
默认从内向外扩散。
<iframe width="100%" height="300" src="//jsfiddle.net/bill_q/cgssdgbc/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

### 兼容性
![兼容性](/assets/blogImg/linear-gradient.png)

### 拓展
关于多背景图，我们可以对其设置多样CSS3动画，并且完全不需要JS的介入。
<iframe width="100%" height="300" src="//jsfiddle.net/bill_q/eb08a191/embedded/html,css,result/dark/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

巧妙运用渐变，会得到一系列意想不到的[图案](http://lea.verou.me/css3patterns/)。

TO BE CONTINUED!

参考文献：
1. https://css-tricks.com/
2. http://www.w3schools.com/
3. http://www.zhangxinxu.com/wordpress/2012/04/base64-url-image-%E5%9B%BE%E7%89%87-%E9%A1%B5%E9%9D%A2%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96/
