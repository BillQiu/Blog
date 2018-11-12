---
title: 浅析 React Fiber
date: 2018-11-12 19:00:33
tags: [react, js, react-fiber]
---

##  引言

在 react 进入大家视野之初，[Virtual DOM](https://reactjs.org/docs/faq-internals.html)（VDOM）的概念让人眼前一亮，在操作真正的 DOM 之前，先通过 VDOM 前后对比得出需要更新的部分，再去操作真实的 DOM，减少了浏览器多次操作 DOM 的成本。这一过程，官方起名 reconciliation，可翻译为`协调算法`。但是 react 发展到今日，随着前端应用的量级越来越大，reconciliation 已经日显疲惫，React Fiber 应运而出。React Fiber 是对 React 核心算法的重写，由 React 团队历时两年多完成。

##  动机

当时被大家拍手叫好的 VDOM，为什么今日会略显疲态，这还要从它的工作原理说起。在 react 发布之初，设想未来的 UI 渲染会是异步的，从 `setState()` 的设计和 react 内部的事务机制可以看出这点。在 react@16 以前的版本，reconciler（现被称为 stack reconciler ）采用自顶向下递归，从根组件或 `setState()` 后的组件开始，更新整个子树。如果组件树不大不会有问题，但是当组件树越来越大，递归遍历的成本就越高，持续占用主线程，这样主线程上的布局、动画等周期性任务以及交互响应就无法立即得到处理，造成顿卡的视觉效果。



理论上人眼最高能识别的帧数不超过 30 帧，电影的帧数大多固定在 24，浏览器最优的帧率是 60，即16.5ms 左右渲染一次。 浏览器正常的工作流程应该是这样的，运算 -> 渲染 -> 运算 -> 渲染 -> 运算 -> 渲染 … 

![img](/assets/blogImg/page1.png)

但是当 JS 执行时间过长，就变成了这个样子，FPS（每秒显示帧数）下降造成视觉上的顿卡。 

![img](/assets/blogImg/page2.png)

那么这个问题如何解决，这就是 fiber reconciler 要做的事了。简而言之可以看下图，将要执行的 JS 做拆分，保证不会阻塞主线程（Main thread）即可。 

![img](/assets/blogImg/page3.png)



## 工作原理

将同步任务拆分大家都能理解，但在拆分之前我们面临以下几个问题：

- 拆什么？
- 如何拆？
- 拆分后的执行顺序如何？



### 拆什么

```
# React@15
DOM 真实DOM节点
-------
Instances React 维护的 VDOM tree node
-------
Elements 描述 UI 长什么样子（type, props）
```

在 react@15 中，更新主要分为两个步骤完成： 1. diff diff 的实际工作是对比 prevInstance 和 nextInstance 的状态，找出差异及其对应的 VDOM change。diff 本质上是一些计算（遍历、比较），是可拆分的（算一半待会儿接着算）。 2. patch 将 diff 算法计算出来的差异队列更新到真实的 DOM 节点上。React 并不是计算出一个差异就执行一次 patch，而是计算出全部的差异并放入差异队列后，再一次性的去执行 patch 方法完成真实的DOM更新。



最后的 patch 阶段更新，是一连串的 DOM 操作，虽然可以根据 diff 后得到的 change list 做拆分，但是意义不大，不仅会导致内部维护的  DOM 状态和实际的不一致，也会影响体验，所以应该做的是对 diff 阶段进行拆分。从下图是 ReactDOM 渲染 10000 个子组件的过程。可以看到，在 diff 执行阶段主线程一直被占用，无法进行其他任何操作 I/O 操作，直到运行完成。

![img](/assets/blogImg/stack.png)



### 怎么拆

由此引出了 React Fiber 的解决方案，以一个 fiber 为单位来进行拆分，fiber tree 是根据 VDOM tree 构造出来的，树形结构完全一致，只是包含的信息不同。以下是 fiber tree 节点的部分结构：

```
{
    alternate: Fiber|null, // 在fiber更新时克隆出的镜像fiber，对fiber的修改会标记在这个fiber上
    nextEffect: Fiber | null, // 单链表结构，方便遍历 Fiber Tree 上有副作用的节点
    pendingWorkPriority: PriorityLevel, // 标记子树上待更新任务的优先级

	stateNode: any, // 管理 instance 自身的特性
    return: Fiber|null, // 指向 Fiber Tree 中的父节点
    child: Fiber|null, // 指向第一个子节点
    sibling: Fiber|null, // 指向兄弟节点
}
```

Fiber 依次通过 return、child 及 sibling 的顺序对 ReactElement 做处理，将之前简单的树结构，变成了基于单链表的树结构，维护了更多的节点关系。

![img](/assets/blogImg/fiber-node.jpeg)



### 执行顺序

Stack 在执行时是以一个 tree 为单位处理；Fiber 则是以一个 fiber 的单位执行。Stack 只能同步的执行；Fiber 则可以针对该 Fiber 做调度处理。也就是说，假设现在有个 Fiber 其单链表（Linked List）结构为 A → B → C，当 A 执行到 B 被中断的话，可以之后再次执行 B → C，这对 Stack 的同步处理结构来说是很难做到的。



在 React Fiber 执行的过程中，主要分为两个阶段（phase）：

1. render / reconciliation (interruptible)
2. commit (not interruptible)



第一个阶段主要工作是自顶向下构建一颗完整的 Fiber Tree， 在 rerender 的过程中，根据之前生成的树，构建名为 workInProgress 的 Fiber Tree 用于更新操作。

<img src="/assets/blogImg/click.png" width="100">



<img src="/assets/blogImg/dom.png" width="300">

假设我有上图所示的 DOM 结构需要渲染，第一次 render 的时候会生成下图所示的 Fiber Tree：





<img src="/assets/blogImg/fiber-tree.png" width="300">



因为我需要对 Item 里面的数值做平方运算，于是我点击了 Button，react 根据之前生成的 Fiber Tree 开始构建workInProgress Tree。在构建的过程中，以一个 fiber 节点为单位自顶向下对比，如果发现根节点没有发生改变，根据其 child 指针，把 List 节点复制到 workinprogress Tree 中。 每处理完一个 fiber 节点，react 都会检查当前时间片是否够用，如果发现当前时间片不够用了，就是会标记下一个要处理的任务优先级，根据优先级来决定下一个时间片要处理什么任务。



> requestIdleCallback 会让一个低优先级的任务在空闲期被调用，而 requestAnimationFrame 会让一个高优先级的任务在下一个栈帧被调用，从而保证了主线程按照优先级执行 fiber 单元。 优先级顺序为：文本框输入 > 本次调度结束需完成的任务 > 动画过渡 > 交互反馈 > 数据更新 > 不会显示但以防将来会显示的任务。



```
module.exports = {  
  // heigh level
  NoWork: 0, // No work is pending.
  SynchronousPriority: 1, // For controlled text inputs. 
  TaskPriority: 2, // Completes at the end of the current tick.
  AnimationPriority: 3, // Needs to complete before the next frame.
  
  // low level
  HighPriority: 4, // Interaction that needs to complete pretty soon to feel responsive.
  LowPriority: 5, // Data fetching, or result from updating stores.
  OffscreenPriority: 6, // Won't be visible but do the work in case it becomes visible.
};
```





在平方运算这一过程中，react 通过依次对比 fiber 节点发现 List，Item2，Item3 发生了变化，就会在对应生成的 workInProgress Tree 中打一个 Tag，并且推送到 effect list 中。

<img src="/assets/blogImg/compare-tree.png" width="500">

<img src="/assets/blogImg/effect-list.png" width="500">



当 reconciliation 结束后，根节点的 effect list 里记录了包括 DOM change 在内的所有 side effect，在第二阶段（commit）执行更新操作，这样一个流程就算结束了。



在这个示例中，详细的比对流程并没有细讲，推荐观看 [Lin Clark](https://www.youtube.com/watch?v=ZCuYPiUIONs) 去年 react conf 中的演讲，非常浅显易懂，本文中示例也来自这个演讲。



## 畅想未来

- 异步渲染 今年在冰岛举行的 JS Conf，Dan 提到了异步渲染的概念，异步渲染不是说一个个加载组件，而是说在以异步的方式加载的同时给人以同步流程的体验，在老设备上，通过牺牲一些加载时间来获得一种流畅的体验。其实在 React@16 版本中，异步渲染默认是关闭的，虽然可以通过 hack 的方式实现非同步，但是因为没有写测试，还是会有 BUG 存在。
- 生命周期大换血 在 react@16 版本中，虽然依旧支持之前的生命周期函数，但是官方已经说明在下个版本中会将废弃其中的部分，这么做的原因，主要是 reconciliation 的重写导致。在 render/reconciliation 的过程中，因为存在优先级和时间片的概念，一个任务很可能执行到一半就被其他优先级更高的任务所替代，或者因为时间原因而被终止。当再次执行这个任务时，是从头开始执行一遍，就会导致组件的某些 `will` 生命周期可能被多次调用而影响性能。react 团队给了我们很长一段时间来处理这个问题，官方也提供了很多参考[案例](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)，可以平滑过渡到下个版本。



react@16 与其说是一个分水岭，不如说是一个过渡，做的很多工作都是在给用户打预防针，告诉你接下来该怎么做，react@17才会是掀起风浪的那一个。reconciliation 的重写给 react 的未来带来太多的可能，包括最近社区讨论的如火如荼的 Hooks，其实也是 Fiber 带来一种可能性。在后续的版本中，个人以为写法上会有不小的改变，主要是为了更加优秀的性能服务；还有就是将一些社区产生的方案做优化，让写法更加人性化（HOC 中的 refs 以及 context 传递），以及对常见的问题给出官方的解决方案（异步数据处理）等等。除了优点，当然也会带来些问题。随着版本的迭代，react 中的概念越来越多，新手学习的曲线只怕是会越来越陡峭。



## 总结

在处理大型应用时，react 的表现不尽人意。主要原因在于计算耗时太长，导致主线程一直被占用，无法处理其他任务。react 团队为了解决这个问题，提出了 Fiber reconciliation 的方案来代替之前的 Stack reconciliation。Fiber 相较于 Stack，采用了异步的方式将之前同步执行的计算过程做拆分，使得主线程不会一直处于被占用的状态，可以有时间去处理其他任务，比如 I/O 操作，交互反馈等。



## 参考文献

- [如何理解 React Fiber 架构？ - 知乎](https://www.zhihu.com/question/49496872)
- [React Fiber - 掘金](https://juejin.im/post/5ab7b3a2f265da2378403e57)
- [图解浏览器的基本工作原理 - 知乎](https://zhuanlan.zhihu.com/p/47407398)
- [React 16 Fiber源码速览 | Zindex’s blog](http://zxc0328.github.io/2017/09/28/react-16-source/)
- [翻譯 React Fiber 現狀確認 – CYB – Medium](https://medium.com/@_cybai/%E7%BF%BB%E8%AD%AF-react-fiber-%E7%8F%BE%E7%8B%80%E7%A2%BA%E8%AA%8D-fd3808072279)
- [完全理解React Fiber | 黯羽轻扬](http://www.ayqy.net/blog/dive-into-react-fiber/)
- [A Cartoon Intro to Fiber](https://www.youtube.com/watch?v=ZCuYPiUIONs)
- [blog/from-jsx-to-dom.md at master · xieyu/blog · GitHub](https://github.com/xieyu/blog/blob/master/React/from-jsx-to-dom.md)
- [Sneak Peek: Beyond React 16](https://reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html)