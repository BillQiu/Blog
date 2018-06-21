---
title: Awesome npx
date: 2018-03-26 17:39:42
tags: [npx, npx]
---

## 背景
在`gayhub`闲逛时，不知怎么突然想看看`create-react-app`（以下简称 create）的源码，来到项目[主页](https://github.com/facebook/create-react-app)，无意中瞄到`README.md`好像发生了一点小小的变化。
> `npx create-react-app my-app`

这个 npx 是什么鬼，突然想起来前几天好像也在一些 npm 的工具包上看到过这个玩意儿，但是没有注意。既然今天又碰到了，那就好好把玩一下，毕竟`create`也用到了它，应该是个值得一用的好东西。看了下项目的修改记录，大约是在两个月前加入使用`npx`的，这也从侧面说明`npx`已基本趋于稳定，可以大胆的使用了。

## 简述
上网简单查阅了下资料，原来 npx 是跟随 npm@5.2.0 发布的，使用5.2.0版本之后的 npm，会自动帮你安上 npx，这么大的推广力度，很难不引起关注啊，之前咋没发现呐？或许是因为好久都没有升级 npm 版本的缘故吧。毕竟本地的 node 版本，不久以前才从 6.x 升级到的 8.x。对于我们这些前端开发人员来说，稳定是最重要的，基本也没有什么切换 node 版本的需求。

以下的总结，基本都是这篇 Medium 上的[文章](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)搬过来的，英语基础不错的同学，可以尝试直接阅读原文。

## npx 带来了什么
### 直接使用项目下的工具包
以前我们使用`create-react-app` 初始化一个项目，大致需要以下步骤：

	1. `$ npm i create-react-app -g`
	2. `$ cd project-directory`
	3. `$ create-react-app my-app`

如果我们使用 npx，并且刚好在想要创建项目的目录下，一行命令就可以搞定：
`npx create-react-app my-project`
npx 帮我们做了什么事呐？

    1. 在当前目录下寻找是否存在 create 包
    2. 如果没有找到，则会去根目录下寻找是否全局安装过 create
    3. 如果还没有找到，就会下载并使用最新版本的 create ，然后初始化项目，完成后，不会有任何多余依赖的残留

相比于之前的使用方式，npx 带给我们的便处是显而易见的。对于 create 这种我们使用频率并不高的工具，其实安装在全局下的必要性并不大。下一次使用的时候，说不定就之前已经迭代好几个版本了。对于 mac 用户来说，容量寸土寸金，而 node_modules 的大小却是不容小觑的。

### 直接运行远端脚本
[gist](https://gist.github.com/) 在日常开发中使用还是比较广泛的，没有程序员愿意一次又一次的写重复写代码。如果我们想执行放在 gist 上的一段 node 脚本，直接用 npx  一行命令即可搞定。
```shell
$ npx https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d32
npx: installed 1 in 5.217s
yay gist
```
需要注意的是，出于安全性考虑，请先通读 gits 上的代码是安全的，再执行命令，就像你要执行一段`.sh`脚本一样小心。

### 作为一个工具包的开发者
我可以直接在`README.md`中写上`$ npx my-tool`，让用户使用我开发工具，这个使用体验可以说是相当舒爽的。
你可以直接体验一些有意思的工具包，happy-birthday，benny-hill，workin-hard，cowsay， yo， create-react-app， npm-check等，详见[awesome-npx 🕶](https://github.com/js-n/awesome-npx)。
也许你担心放在 `snpm`上的工具无法使用，其实大可放心，用`nrm`等工具指定下源即可完美下载使用。或者麻烦一点就这样写：`npx --registry=http://registry.npm.souche-inc.com @souche-f2e/sad-cli -h`，指定 registry 即可。


### shell auto-fallback
这个不知道翻译合适，就先不翻译了🤔。
其作用就是让我们**方便地**使用不同版本的依赖包，比如我本地安装的是 babel@6.x，但是我想使用 babel@5.x 看看执行效果，直接执行`babel@5 filename.js`即可。
```shell
$ bebel@5 -V
babel@5 not found. Trying with npx...
5.8.38 (babel-core 5.8.38)
```

当然想只用这个功能，还是需要做一些配置的：
将对应的代码添加到对应的文件中即可， `~/.bashrc, ~/.zshrc, ~/.config/fish/config.fish`。
```
For bash@>=4:
$ source <(npx --shell-auto-fallback bash)

For zsh:
$ source <(npx --shell-auto-fallback zsh)

For fish:
$ source (npx --shell-auto-fallback fish | psub)

```

## 参考链接
* [Introducing npx: an npm package runner – Kat Marchán – Medium](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)

自由转载-非商用-非衍生-保持署名[（创意共享3.0许可证）](https://link.juejin.im/?target=https%3A%2F%2Fcreativecommons.org%2Flicenses%2Fby-nc-nd%2F3.0%2Fdeed.zh)
