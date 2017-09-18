---
title: background八日谈第二日：background-color
date: 2016-05-05 10:56:10
tags: [css, background, background-color, RGBA, HSLA]
---
## 三种显示颜色的方式
![颜色](/assets/blogImg/color-style.png))
<!-- more -->
### HEX
先说说HEX，最常见的颜色表达方式。
PS: 此HEX非彼hex(贺老)
```
html {
  background-color: #fffff;
}
```
上述代码中的`#fffff`就是HEX的颜色描述方式，也可以简写成`#fff`，一看就能明白的东西，不多说了。
### color name
如果想要用白色作为背景色，可以这样写:
```
html {
  background-color: white;
}
```
因为颜色的多样，并不是没种颜色都可以通过`color name`来表示。在实际开发中用的比较好，更多用在调试中。
想要知道更多颜色的名字，可以看[这里](https://css-tricks.com/snippets/css/named-colors-and-hex-equivalents/)。
### RGBA
RGBA的颜色描述方式大家肯定也很熟悉了
```
html {
  background-color: rgba(0, 0, 0, 0.8); /* 80% Black */
}
```
R:red, B:blue, G:green, A:alpha 通过三原色的配比获取我们想要的任何颜色，通过alpha值调节颜色的透明度。
`RGBA`的浏览器支持性很好，除了IE8及以下浏览器都支持这个属性。
如果项目不需要支持IE8及以下浏览器，但是用到了RGBA，可以使用一个*Fallback*实现向下兼容。
```
html {
    background-color: rgb(0, 0, 0, 0.8);  /Fallback/
    background-color: rgba(0, 0, 0, 0.8); /* 80% Black */
}
```
当需要在IE8及以下浏览器中实现RGBA的效果时，就需要用到`Microsoft CSS filter`了
```
<!--[if IE]>
   <style type="text/css">

   .color-block {
       background:transparent;
       filter:progid:DXImageTransform.Microsoft.gradient(startColorstr=#50990000,endColorstr=#50990000);
       zoom: 1;
    }
    </style>

<![endif]-->
```

### HSLA
这种颜色的表达方式比较少见，至于为什么，我也清楚。有国外的大神说可能是因为`HSLA`的色值比较难获取。
Hue、Saturation、Lightness and alpha分别代表色调、饱和度、亮度和透明度。
从理性的角度来说，用HSLA更容易创造出自己想要的颜色。
1. 改变第二个数值，会使颜色的饱和度发生变化。
2. 改变第三个数值，使颜色的亮度发生变化。

```
#some-element {
  background-color: hsla(170, 50%, 45%, 1);
}
```
`HSLA`的兼容性与RGBA相同，可以使用RGBA的地方都可以是使用HSLA代替。
<iframe id="rewYxL" src="http://caniuse.com/css3-colors/embed" scrolling="no" frameborder="0" height="300" allowtransparency="true" allowfullscreen="true" class="cp_embed_iframe undefined" style="width: 100%; overflow: hidden;"></iframe>


TO BE CONTINUED!

参考文献：
[css-tricks](https://css-tricks.com)
