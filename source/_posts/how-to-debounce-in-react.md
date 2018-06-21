---
title: 在 react 组件中使用 debounce 函数
date: 2017-10-15 15:49:25
tags: [react, debounce]
---

## 什么是 debounce

**debounce**，中文翻译为防抖。与之相对应的还有一个概念 **throttle** —— 节流。两者用来控制某个函数在一定时间内执行多少次的技巧，相似却又不同。

关于两者的对比以及使用场景，可以阅读这篇[文章](https://css-tricks.com/debouncing-throttling-explained-examples/)来加深了解。本文主要讲述的，是如果在 `react components` 中，使用 `debounce` 函数来防止回调事件的多次触发，从而提升代码效率。



## 应用

在一个 `input` 输入框中，给其绑定了一个 `onChange` 事件，每次输入后，都会触发一个回调函数。因为我们想要的只是用户输入的最后结果，所以除了用户输入完成后触发的回调函数，其他都是冗余的。因此，需要对绑定的回调函数做 `debounce` 处理。

关于  `debounce` 函数，有很多实现版本，这里选用了 `loadsh.debounce`。虽然函数实现原理很简单，但是用于生产环境的代码，还是建议使用成熟的第三方库。

我们的第一反应，一般都是直接将回调函数用 `debounce` 包裹起来达到想要的效果。

```jsx
import react, { Component } from 'react';
import { debounce } from 'lodash.debounce';

export default class Debounce extends Component {
  printChange(e) {
    console.log('value :: ', e.target.value);
    // call ajax
  }
  render() {
    return (
      <div>
        <input onChange={debounce(this.printChange, 500)} />
      </div>
    );
  }
}
```

但是这么做之后，浏览器就会报异常： `Uncaught TypeError: Cannot read property 'value' of null`。为什么会这样？这里就涉及到了 `react` 事件系统 中的一个概念：合成事件。

### 合成事件（SyntheticEvent）

事件处理程序通过 合成事件（SyntheticEvent）的实例传递，`SyntheticEvent` 是浏览器原生事件跨浏览器的封装。`SyntheticEvent` 和浏览器原生事件一样有 `stopPropagation()`、`preventDefault()` 接口，而且这些接口夸浏览器兼容。

### 事件池（Event Pooling）

`SyntheticEvent` 是池化的. 这意味着 `SyntheticEvent` 对象将会被重用，并且所有的属性都会在事件回调被调用后被    nullified。 这是因为性能的原因。 因此,你不能异步的访问事件。

通过了解事件系统，也就不难理解为什么会报错了。因为经过 `debounce` 包装后的回调函数，变成了一个异步事件，在池化后被 nullified 了。

那么怎样才能解决这个问题？

通过在回调事件顶部加上 `e.persist()` 就可以从池中移除合成事件，并允许对事件的引用保留。

```jsx
import react, { Component } from 'react';
import { debounce } from 'lodash.debounce';

export default class Debounce extends Component {
  printChange(e) {
    e.persist();
    debounce(() => {
      console.log('value :: ', e.target.value);
      // call ajax
    });
  }
  render() {
    return (
      <div>
        <input onChange={this.printChange} />
      </div>
    );
  }
}
```

这样做之后报错问题虽然解决了，但是会发现 `debounce` 的函数并没有被执行。因此，还需要对回调函数进行优化。

把需要异步执行的回调函数抽离出来封装，并且在组件初始化话的时候就将其 `debounce` 化，就可以得到我们想要的效果。

```jsx
import react, { Component } from 'react';
import { debounce } from 'lodash.debounce';

export default class Debounce extends Component {
  construtor() {
    super();
    this.callAjax = debounce(this.callAjax, 300);
  }
  
  callAjax = (value) => {
    console.log('value :: ', value);
    // call ajax
  }
  printChange(e) {
    e.persist();
    this.callAjax(e.target.value);
  }
  render() {
    return (
      <div>
        <input onChange={this.printChange} />
      </div>
    );
  }
}
```

## 参考文章

*   [JavaScript 函数节流和函数去抖应用场景辨析 · Issue #20 · hanzichi/underscore-analysis · GitHub](https://github.com/hanzichi/underscore-analysis/issues/20)
*   [Debouncing and Throttling Explained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)
*   [SyntheticEvent](https://reactjs.org/docs/events.html)