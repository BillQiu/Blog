---
title: react 中的浅比较
date: 2018-12-11 16:55:23
tags: [react]
---

浅比较（shadow comparison）是 react 中经常会提到的一个概念，比如我们使用`PureComponent`时，组件通过改变`shouldComponentUpdate`生命周期，自动检测`props`和 state 是否发生变化来决定是否重渲染。在这个过程中，对`props`和`state `就执行了浅相对（shallowEqual）判断。

重点在于如何理解“浅”字，通过阅读[源码](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/core/shallowEqual.js#L39-L67)可以得出一个确切的结论。



如何通过浅比较 A, B 得出是否相等：

1. 通过 [Object.is()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) 方法比较值 A, B 是否相等，如果相等，返回`true`。

   比较结果详见 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness)。考虑兼容性，react 使用了 Polyfill 。

2. 如果 A, B 中有个任何一个为`null`或者数据类型不是`object`，直接返回`false`。

   Object.is 方法用于对**基本数据类型**做一个精确的比较，如果发生误判，仅可能是 object 类型。但如果其中一个不是 object，就不可能相等了。

3. 如果 A, B 包含的顶层键值对数量不同，直接返回`false`。

4. 将 A, B 中的键值对一一对比，如果 B 存在 A 没有的键或者相同键对应的值不同，就返回`false`。

5. 使用反证法，通过所有不相等判断之后，只能是相等的，返回`true`。



源码其实很简答就能看明白，重点是对浅比较这个过程有了一个更清晰的了解。就拿`PureComponent`来说，之前我广义上的理解是：如果更新一个对象或数组，可能会发生一些渲染上的问题。现在可以清楚表达为：不能通过直接修改之前的引用值，而需要通过重新声明一个变量的方式（`Array.prototype.concat(), Object.assign()`），才能触发重渲染。