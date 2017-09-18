---
title: 在mac上使用docker部署gitlab
date: 2016-07-08 10:51:37
tags: [git, gitlab, docker, 部署]
---
## 前言
git，当下如日中天的版本控制工具。
无奈公司用的是SVN，在Cornerstone上用鼠标update、commit、Check Out实在不是很爽。但是有不想花时间去学习SVN命令，便打算在自己的mac上装个gitlab来协助自己平时的开发任务。

之前我尝试安装gitlab已经好多次了，但是苦于自己的英语捉急，又没有很友好的中文文档，gitlab对mac的支持有限，多是针对linux的应用环境。

后来无意中接触到了docker，一通摸索之后稍微摸着了点门道，也算是成功地部署了gitlab在自己的mac上。
<!-- more -->
## 准备
docker最近出了mac的beta版，可以想原生的mac APP一样使用，大大降低了部署的门槛，给个[传送门](https://download.docker.com/mac/beta/Docker.dmg)。

docker安装完成后，需要下载Docker镜像管理工具Kitematic并安装。
![Kitematic](/assets/blogImg/Kitematic.png)

## 部署
以上两个工具都安装好后，就进入正式部署的环节。
打开Kitematic，单击new，搜索gitlab-ce，看到第一个官方提供的镜像(image)，单击creat，Kitematic就会自动帮你部署gitlab所需要一切环境。
![gitlab-ce.png](/assets/blogImg/gitlab-ce.png)

部署完成后，Kitematic会出现下述页面，通过单击WEB PREVIEW中的箭头，可以跳转到浏览器管理已经部署好的gitlab。
![gitlab-ce-ok](/assets/blogImg/gitlab-ce-ok.png)

因为是第一次登陆，所以需要设置一个管理员密码。
![gitlab-ce-login](/assets/blogImg/gitlab-ce-login.png)

之后，在下面的注册框中注册完一个账号后就会自动登陆gitlab。在这里，你就可以随意把玩git了。
![gitlab-ce-new](/assets/blogImg/gitlab-ce-new.png)
![gitlab-in](/assets/blogImg/gitlab-in.png)

## 新建一个项目
单击首页的New Project按钮进入一个新建项的页面，和github的页面大同小异。
![gitlab-newproject](/assets/blogImg/gitlab-newproject.png)

我新建了一个名为test的项目，下一步就需要把该项目clone到本地进行操作了。
在此之前，必须要设置好SSH KEY才可以push或者pull这个项目。
在项目页面中也给我们这个提示。
至于如何设置，用过github的同学肯定清楚，在gitlab中也给了我们很详细的提示，这里就不多说了。
有很重的要一点需要说下的是，在执行`clone`命令时，如果网址是通过localhost打开的，一定要把localhost换成本地的IP地址`127.0.0.1`，不然clone命令是会失败的。而且一定要在clone的地址后加上`.git`，不然在push代码的时候是会失败的。
举个例子：如果你当前项目的地址是`http://127.0.0.1:32772/bill/test`
clone的时候需要改成`http://127.0.0.1:32772/bill/test.git`才可以。

## 小结
因为我也是第一次接触docker，在部署方面的知识有限，对git的也只是会几个简单的命令，所以不能保证这个过程大家都可以顺利执行下来。如果错误，请多多包涵！

参考文献：
[基于Docker搭建Gitlab/Gitlab CI测试环境](http://chrisrc.me/autoit/2016/05/12/autoit-gitlab-gitlab-ci-mac/)
