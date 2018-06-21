---
title: babel-polyfill VS babel-runtime
date: 2018-02-28 18:15:22
tags: [Babel]
---

<img src="http://img.souche.com/f2e/b52b1660f95dc4849402e60c79befcdb.png">

## 背景

在项目迭代过程中，因为有兼容 IE 的需求，根据文档使用`babel-polyfill`和`babel-runtime`两个插件解决问题。但是对于二者之间的恩怨情仇，却不甚了解，便打算细细探究一番。

## 关于 Babel
如果我们没有配置一些规则，Babel 默认只转换新的 JavaScript 句法（syntax），而不转换新的 API，比如 Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise 等全局对象，以及一些定义在全局对象上的方法（比如 Object.assign ）都不会转码。
所以，当这样的代码出现时：
```js
const key = 'babel'
const obj = {
    [key]: 'foo',
}
```
Babel 默认会编译成下面的代码
```js
function _defineProperty(obj, key, value) {
    if (key in obj) {
        Object.defineProperty(obj, key, { value: value, enumerable: true, configurable: true, writable: true });
    } else {
        obj[key] = value;
    }
    return obj;
}

var key = 'babel';
var obj = _defineProperty({}, key, Object.assign({}, { key: 'foo' }));
```
我们可以看到代码中多了一个名为`_defineProperty`的帮助函数，但是这个帮助函数仅仅在当前模块中生效，因此其他模块中如果用到了同样的语法，编译后就会出现大量的重复代码。

## babel-polyfill
原理是当运行环境中并没有实现的一些方法，babel-polyfill 会给其做兼容。
但是这样做也有一个缺点，就是会污染全局变量，而且项目打包以后体积会增大很多，因为把整个依赖包也搭了进去。所以并不推荐在一些方法类库中去使用。
### 用法
	1. `npm install --save babel-polyfill`
	2. 在应用的入口引用，以确保它能够最先加载：
	`import "babel-polyfill";` 或者
	`require("babel-polyfill");`

## babel-runtime
为了不污染全局对象和内置的对象原型，但是又想体验使用新鲜语法的快感。就可以配合使用`babel-runtime`和`babel-plugin-transform-runtime`。
比如当前运行环境不支持`promise`，可以通过引入`babel-runtime/core-js/promise`来获取`promise`，
或者通过`babel-plugin-transform-runtime`自动重写你的`promise`。也许有人会奇怪，为什么会有两个`runtime`插件，其实是有历史原因的：刚开始开始只有`babel-runtime`插件，但是用起来很不方便，在代码中直接引入`helper` 函数，意味着不能共享，造成最终打包出来的文件里有很多重复的`helper`代码。所以，`Babel`又开发了`babel-plugin-transform-runtime`，这个模块会将我们的代码重写，如将`Promise`重写成`_Promise`（只是打比方），然后引入`_Promise helper`函数。这样就避免了重复打包代码和手动引入模块的痛苦。

### 用法
	1. `npm install --save-dev babel-plugin-transform-runtime`
	2. `npm install --save babel-runtime`
	3. 写入 `.babelrc`
```json
{
  "plugins": ["transform-runtime"]
}
```
启用插件`babel-plugin-transform-runtime`后，`Babel`就会使用`babel-runtime`下的工具函数，转译代码如下：
```js
'use strict';

var _defineProperty2 = require('babel-runtime/helpers/defineProperty');

var _defineProperty3 = _interopRequireDefault(_defineProperty2);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

var key = 'babel';
var obj = (0, _defineProperty3.default)({}, key, 'foo');
```

### 不足
`babel-runtime` 不能转码实例方法，比如这样的代码：
```js
'!!!'.repeat(3);
'hello'.includes('h');
```
这只能通过 babel-polyfill 来转码，因为 babel-polyfill 是直接在原型链上增加方法。

## And more
随着历史进程的发展，新一代的 [babel-prenset-env](https://github.com/babel/babel/tree/master/packages/babel-preset-env) 很强大，了解一下😜

## 参考链接
* [babel 教程](https://blog.zfanw.com/babel-js/#x3-babel-runtime)
* [Babel 全家桶 · Issue #20 · brunoyang/blog · GitHub](https://github.com/brunoyang/blog/issues/20)
* [Runtime transform · Babel](https://babeljs.io/docs/plugins/transform-runtime/)
* [Polyfill · Babel](https://babeljs.io/docs/usage/polyfill/#usage-in-node--browserify--webpack)

自由转载-非商用-非衍生-保持署名（[创意共享3.0许可证](https://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)）