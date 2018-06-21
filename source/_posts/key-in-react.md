---
title: 谈谈 react 中的 key
date: 2018-01-25 14:15:15
tags: [react, js, key]
---

## 前言

![key-warn](http://img.souche.com/f2e/cf0f9218d8effa21cf4d291a77db6c7c.jpg)

如果你用过 react，并且曾经尝试遍历数组来渲染一个组件，就应该遇到过上面的提示。因为提示的等级为 **Warning**，而非 Error，所以很多开发同学可能就不会去在意，包括我自己。在前几天开发一个需要动态渲染的组件时，才发现的了 key 的妙用，也因此打算研究下 key 到底是干什么用的。



## 定义

>   Keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a **stable** identity.

key 是用来帮助 react 识别哪些内容被更改、添加或者删除。key 需要写在用数组渲染出来的元素内部，并且需要赋予其一个**稳定**的值。稳定在这里很重要，因为如果 key 值发生了变更，react 则会触发 UI 的重渲染。这是一个非常有用的特性。



### key 的唯一性

在相邻的元素间，key 值必须是唯一的，如果出现了相同的 key，同样会抛出一个 Warning，告诉相邻组件间有重复的 key 值。并且只会渲染第一个重复 key 值中的元素，因为 react 会认为后续拥有相同 key 的都是同一个组件。



### key 值不可读

虽然我们在组件上定义了 key，但是在其子组件中，我们并没有办法拿到 key 的值，因为 key 仅仅是给 react 内部使用的。如果我们需要使用到 key 值，可以通过其他方式传入，比如将 key 值赋给 id 等。

```jsx
arr.map(item => <Component key={item.id} id={item.id} />)
```



## 反模式

很多时候，我们可能并没有在遍历数组渲染组件的时候写上 key 的习惯，因为除了控制台报到一个 Warning，并不会有任何影响。因为赋 key 值这一步 react 帮我们做了，默认使用的是遍历过程中的 index 值。

```jsx
let arr = ['first', 'second'];

// list1 和 list2 是等价的
const list1 = arr.map(item => <p>{item}</p>);
const list2 = arr.map((item, index) => <p key={index}>{item}</p>);
```

 在上面的例子中，如果数组发生了变化，我们需要在数组的末尾插入一个元素 `arr.push('third')`，react 经过 diff 后就会发现 ：key 值为0和1的元素并没有发生如何变化，所以 react 会认为， 最后需要在 UI 上发生变更，仅仅是插入一个 key 值为2的新元素。

但是如果我们在数组的开头插入了一个新元素`arr.unshift('zero')`，react 经过 diff 后就会发现每一个元素的 key 值都发生了变化，也是就说每个元素都要重新渲染一次，虽然从结果来看，仅仅是在开头添加了一个元素而已。如果负责渲染的数组数据量较大的话，则会对性能造成较大的影响。与 react 使用的启发式算法是相悖的。

```html
<!-- before -->
<p key="0">first</p>
<p key="1">second</p>

<!-- after -->
<p key="0">zero</p>
<p key="1">first</p>
<p key="2">second</p>
```

因此，推荐的做法是每个兄弟元素都加上一个稳定唯一的 key 值。



## 应用

主要应用在 **dynamic stateful components**中，也就是需要动态渲染的子组件。

下图是一个名为 `Filters`的组件，内部由一个个`condition`子组件组成，由 `defaultValue`负责初始化渲染。`defaultValue`的值可由组件生成，可以通过 `onChange`事件获取，默认不传为空数组。通过将之前保存好的值回传给组件，即可渲染需要的 `Filters`。

```js
const defalutValue = [{
  id: 1515134128441,
  tag: 'mobile',	// 手机号 决定最后需要使用的 input 输入框
  flag: [
    '身份标签',
    `{"code":"location_province_code","valueType":"area_province"}`
  ],                // 决定第一级 级联选框 的默认值
  operator: "neq",  // 操作符
  target: "00001"   // input 输入框的值
}, {
    ...
}]
```



<img src="http://img.souche.com/f2e/88f2b0b7f99e637d275b49c4addd1c38.jpg">

这里遇到的问题是，`condition` 子组件的内部已经比较复杂了，需要处理多种情况：

1.  默认值的渲染
2.  不同筛选条件要对应不同的赋值符以及条件框
3.  按照级联的逻辑，父级的变更需要判断子级的状态是否需要改变

在上述情况中，如果`condition`要实现接收不同的默认值来展示不同的效果，则需要写一系列复杂的 `componentWillReceiveProps`生命周期。而且不能保证代码的可读性以及维护性，是一个很可怕的事情。

但是，如果你在用 `defaultValue`渲染每个 `Condition` 的时候，给它加一个唯一稳定的 key 值，就可以完美解决这个问题。我在实现的过程中，用了当前时间戳作为`Condition`的 key 值，保证其唯一稳定性。在删除、增加标签都能确保正确渲染。如果没有 key 值也没有写`componentWillReceiveProps`生命周期，在删除的时候就会发生渲染错误。

简而言之，改变 key 值来重渲染组件是一种——相较于复杂`componentWillReceiveProps`生命周期——十分低成本的方式。

## 参考链接

-   [[React技术内幕] key带来了什么](https://juejin.im/post/59abb01c518825243f1b6dad)
-   [List and Keys](https://reactjs.org/docs/lists-and-keys.html)