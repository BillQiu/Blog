---
title: 如何获取CSS的属性值
date: 2016-02-24 15:28:18
tags: [JavaScript, jQuery]
---
## 前言
说到通过JS获取CSS的属性值，大家首先想到的一般是通过`element.style.attr`方法来获取。
或者通过jQuery的`.css()`获取。
但是不知道你有没有想过，jQuery是怎么获取的呐？
其实我也没有想过这个问题 = = 
只是今天在学习JS动画的时候，碰到了JS的`getComputedStyle`方法，通过查阅一系列的资料，总结了以下几种获取CSS属性值的方法。

## 概述
在这篇博文中，我打算介绍三种获取CSS属性值的方法。对于只想知其然的朋友，可能会觉得比较啰嗦。但是个人认为在对一个问题钻研的越深入，在下次碰到时才能举一反三，迎刃而解。
1. 通过jQuery的`.css()`获取属性
2. 通过原生JS的`element.style.attr`方法获取
3. 通过`getComputedStyle()`获取
<!--more-->
## jQuery方法
**.css( propertyName )**
通过`.css("background-color")`可以获取元素的背景颜色，同时用`.css("backgroundColor")`也可以获取元素的背景颜色。其中的"background-color"是用multiple-word格式化的DOM属性的写法，而在JS中获取背景颜色，通过以下代码：
```
document.body.style.backgroundColor;
```
需要**注意**的是，**简写速写**的CSS属性(例如： margin, background, border) 是不支持的，例如，如果你想重新获取margin，可以使用$(elem).css('marginTop') 和 $(elem).css('marginRight')，其他的也是如此。


从jQuery1.9开始，添加了`.css( propertyNames )`,可以匹配元素的多个属性值。
```
$( elem ).css([ "borderTopWidth", "borderRightWidth", "borderBottomWidth"; "borderLeftWidth" ])
```

## 原生JS方法
**1. style方法**
通过element.style不仅可以获取元素的属性值，还可以设置属性值，可读可写。

**2. getComputedStyle方法**
> The getComputedStyle() method gets all the actual (computed) CSS property and values of the specified element.The computed style is the style acutally used in displaying the element, after stylings from multiple sources have been apllied.

简单翻译下就是说getComputedStyle方法返回指定元素所有CSS属性和值，也就是最终显示出来的样式值。相比于style方法，getComputedStyle返回一个CSS样式声明对象([object CSSStyleDeclaration])，**只读**。还有一点很大的区别，getComputedStyle返回的是最终显示在页面上的样式的属性值。

> window.getComputedStyle(element, pseudoElement)

**getPropertyValue方法**
getPropertyValue方法可以获取CSS样式申明对象上的属性值（直接属性名称）
```
window.getComputedStyle(element, null).getPropertyValue("float");
```
如果不使用getPropertyValue方法，也可以使用键值的方式访问。
```
window.getComputedStyle(element, null).[];
```
但是使用键值存在一个问题，要获取float属性时，需要判断使用cssFloat还是还是styleFloat(for IE)，比较麻烦。
由于getPropertyValue不支持驼峰命名法，当遇到用“-”连接的属性时，：
```
style.getPropertyValue("border-top-left-radius");
```
getPropertyValue方法虽然好用，但是是DOM2的方法，IE9以上才支持。So。。。
![DOM2](/assets/blogImg/DOM2.png)
<br>
**getAttribute方法**
![getAttributeSuport](/assets/blogImg/getAttributeSuport.png)
从上图可以看出，[getAttribute方法](http://www.w3schools.com/jsref/met_element_getattribute.asp)对浏览器全支持。
张老师给的[例子](http://www.zhangxinxu.com/study/201205/getpropertyvalue-getAttribute-background-color.html)
需要注意的一点是，getAttribute方法需要属性名用驼峰写法
```
style.getAttribute("backgroundColor");
```
并且不需要`cssFloat`与`styleFloat`的兼容写法，可以直接float
```
style.getAttribute("float");

```
<br>
**getComputedStyle的兼容性**
![ getComputedStyle的兼容性](/assets/blogImg/getComputedStyle的兼容性.png)
从上图中，可以看出getComputedStyle是不支持IE8及以下浏览器的。
但是IE自己提供了一个currentStyle方法。在查阅相关资料时，Mozilla开发者中心是这样描述的：
> Element.currentStyle is a property which is a proprietary alternative to the standardized window.getComputedStyle method. It is available in old versions of Microsoft Internet Explorer.

如果有使用需要的，可以去微软的开发者中心查阅。  https://msdn.microsoft.com/en-us/library/ms535231(v=vs.85).aspx

**3. style与getComputedStyle的区别**

下面看一个例子:
```
<body>
	<p id="p"></p>
	<script>
		window.onload = function() {
			var p = document.getElementById("p");
			var calcWidth = getComputedStyle(p,null)["width"];
			console.log("pWidth = " + p.style.width);  //0
			console.log("calcWidth = " + calcWidth);   //与浏览器宽度相关
		};
	</script>	
</body>
```
p元素为块级元素，所以宽度为浏览器视口的宽度(减去各种边距值)。
<br>


参考文献：
1. http://www.zhangxinxu.com/wordpress/2012/05/getcomputedstyle-js-getpropertyvalue-currentstyle/
2. http://www.w3schools.com/jsref/jsref_getcomputedstyle.asp
3. http://www.jquery123.com/css/




