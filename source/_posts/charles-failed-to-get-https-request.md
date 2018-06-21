---
title: 解决在 IOS 上 使用 Charles 抓取 HTTPS 请求失败的问题
date: 2017-12-13 21:53:34
tags: [charles]

---

## 背景
在看了一大堆教程，配置好 Charles 和 iPhone 后，还是无法解析 HTTPS 请求。证书，ip 地址，端口号，白名单，字符编码，能做的都做了，还是没有一点头绪。

其实这个问题的解决方案很简单，但是网上搜了很多，都找不到一篇合适的中文教程，所以在这里给各位想在 iPhone 上，用 Charles 抓取并解析 HTTPS 请求的同学指一条明路。

在 IOS 10.3 版本之后，新增了一条安全策略。单单在 iPhone 上安装 Charles 证书是不够的，因为安装证书之后，默认是关闭，需要通过以下路径打开才可以捕获并解析 HTTPS 请求：
> Settings > General > About > Certificate Trust Settings > Toggle the cert to on.
> 设置 > 通用 > 关于 > 证书信任设置 > 启用证书

对，就是这么简单，至于如何使用 Charles 在 iPhone 上抓包，网上教程有很多，这里就不在展开了。
## 参考链接
[Charles failed to get https requests](https://stackoverflow.com/questions/43462511/charles-failed-to-get-https-requests)