---
title: Vue 真的比 React 上手简单吗?
date: 2018-04-12 16:57:58
tags: [react, vue]
---

>   本文源自我在知乎的一个回答：
>
>   [Vue 真的比 React 上手简单吗?](https://www.zhihu.com/question/271908748/answer/365118404)

在我接触 Vue 之前，完全没有用过其他 mvvm 框架，理解一点模块化的概念，用过 sea.js。当时入坑前端时间不长，js 的基础相对较差，就是这样的背景下我进入公司开始用 Vue 写业务。

1.  完善的中文文档

    对于一个入门的小白来说，这点很重要。作为一个有素质的开发，我更愿意去官网看文档，而 react 的官方文档是英文的，这对我后续使用 react 提升了不小的门槛。

    ​

2.  API 设计

    在 Vue 的文档中，提供了很多易用的 API ，比如说`vue-if`、`vue-for`、`vue-show`等等。一个萝卜一个坑，对开发者来说，"坑"已经在那边了，就等你把萝卜塞进去就可以了。而 react 不一样，react 更强调的是一种灵活性，通过在 jsx 中直接写 js 来达到我想要的渲染效果。对于刚入门的同学，js 基础可能没那么扎实，所以 Vue 的 API 提供了很大的帮助。

    ​

3.  设计理念

    我当初在写 Vue 的时候，对组件化的开发模式理解比较浅，所以在做项目时，一个页面直接都写在一个`.vue`文件中，用的还是之前 jQuery 时代的开发思想。但是这样做其实关系也不大，就是开发效率可能比较低下。

    Vue 是一个典型的 mvvm 框架，数据的变化会直接反映在视图上，这对一个新手来说是很舒服的，我直接把数据变了，我的目的就达到了，至于其他的事：比如“组件之间通信”、“生命周期”等老生常谈的问题暂时可以不考虑。其实我当初入门写了半年时间的 Vue，也不太清楚 Vue 的生命周期具体是怎么样的。

    而 react 不同，react 更强调的是一种组件化开发的概念。所有页面都是组件的组合，好的组件需要符合『高内聚，低耦合』的设计理念。其中最基础的触发视图更新的方法`setState`也有不小的坑，最为人津津乐道的就是它是一个异步的过程。Twitter 上还为此打过一场口水战，有人提议不要使用`setState`，而使用 redux 等单向数据流的概念，使每一个更新步骤都是可追踪的。这对于新手来说，无疑又是一道坎。在开发 react 的过程中，对生命周期的掌握程度也是一大问题。举个最简单的例子，我向一个子组件传递一个新的 props，但是却没有触发视图的更新，其实就是因为用了 stateful components，但是没有写`componentWillReceiveProps`生命周期。

    这些都是刚开始使用 react 时会遇到的问题，而我用 Vue 就不会。

    ​

4.  概念的堆砌

    在 react 中，你会接触到很多“专业术语”。`HOC`、`stateless`、`render props`。其实这也是我很喜欢的 react 的一个原因，它提出的一些设计理念，让我感觉到他很先进，当然，他确实也很先进。经常会有人提出一些有意思的概念和方案， `css in js`、`flux` etc...

也许因为自己 react 写的比较多，在我眼里，react 的生态是明显要优于 Vue 的，当然，这对小白开发者来说，其实影响不大，但是越到后来，一个框架的生态决定了其上限，还是一个很需要考量的因素。

最后再说几句，我是在写了大半年 Vue 后转的 react，但是当时感觉上手 react 的难易程度，还是明显要高于刚写 Vue 的时候的。