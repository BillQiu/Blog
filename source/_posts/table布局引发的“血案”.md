---
title: 给table交的一点学费
date: 2016-04-12 19:38:42
tags: [table, display, 盒模型]
---
最近在做公司的一个项目，项目团队还挺大，整个团队加起来有10多个人，向我这样的小前端也就只有打打杂的份了 = =
以前做的都是一些静态页面为主的工作，这次的项目则需要前后端交互，各种接口的调用，一个完整web项目从零开始，对我来说真是一个很好的学习的机会。

项目前期的工作当然还是以搭静态页面为主，其中必然遇到了不少的坑，且听我慢慢道来。

## 关于table布局
现代的网页，已经很少看到`table`元素了，除非是非常标准的表格，用`table`实现效率远高于其他布局方式，不然，永远不推荐使用`table`！永远不推荐使用`table`！！永远不推荐使用`table`！！！
在这里向当年使用table布局的老同志们致以深深的敬意。

display大家肯定都知道，我是一个喜欢刨根问底的人，曾经查阅过display相关的文档资料，其中有将近有一半的属性和table有关，因为很少用到，又嫌麻烦，便没有去做多深入了解，知道今天遇到了，才被好好地坑了一把。

![table](/assets/blogImg/table.png)
<!-- more -->
上面是我整理的一些table标签和相关display的脑图，看晕了没？
既然说到了display，就不得不说W3C标准盒模型
![boxmodel](/assets/blogImg/boxmodel.png)
这是一个标准的W3C盒模型图，内容(content)+边框(border)+内边距(padding)+外边距(margin)。
在早期的IE浏览器中，存在一个怪异盒模型。
![IEbox](/assets/blogImg/IEbox.png)
如上图所示，标准盒子模型中content = 怪异盒模型中content+padding+border。
换句话说，在怪异盒模型中，如果你设置了border值和padding值，会呈现出一个比遵从标准行为的结果更窄或者更短的盒子。
再换个角度来说，当我们在标准盒模型中给一个元素定宽时，如果给它设置了padding和border，就会得到一个比实际更宽的效果。如果要得到想要的效果，就需要在宽度值中减去padding和border的值。
为了避免上述情况，`box-sizing: border-box; `横空出世，demo可以戳[这里](http://zh.learnlayout.com/box-sizing.html)。

在table中，常用的tr,td,th,tbody都是不符合标准盒模型的，所以一些常用的CSS属性是无法设置的，也就导致了无法设置一些常用样式。比如：
- background 是正常的，除了tbody、tfoot、thead都可以用，
- text-align、text-indent 等文字相关属性，只对单元格有效，但是可以被继承。 \*margin 只在 table 上有效，对 td th 等等无效。
- border 只在 table、td、th、caption 有效，对 thead、tbody、tfoot、tr 等无效。
- padding、overflow、vertical-align 只对单元格有效。
- height 在表格、行和单元格上有效，但其值只能作为最小高度，因为内容总是会扩大单元格、行以及表格的高度。如果使用 display 将其改成 block 块级元素，就会溢出而不会扩大。

table中还有一个大坑就是设置表格线框。
一般我们在表格内画线框，都是为`td`设置`border`属性。
今天在做项目时，却碰到了这样一个问题，
![point](/assets/blogImg/point.png)
没错，就是中间那一个小小的像素点，差点没我搞死。
如果你的表格基础不错，应该一眼就能看出问题的所在。
如果你真看不出，可以先想想为什么会有这一个像素点的存在。
表格的是典型的“井”字形布局，有横就有竖。想到了吗，那一点就是默认存在的白色竖边框。
```
<table cellspacing="0">
```
使用以上代码完美解决问题。
这里有一点需要注意的是，在HTML5中，已经移除了这个属性，推荐使用CSS的`border-spacing`来设置。

以上就是给`table`交的一点学费，与君共勉！

<br>
参考文献：
1. https://zh.wikipedia.org/wiki/IE%E7%9B%92%E6%A8%A1%E5%9E%8B%E7%BC%BA%E9%99%B7
2. http://yujiangshui.com/about-table/
3. http://zh.learnlayout.com/box-model.html
