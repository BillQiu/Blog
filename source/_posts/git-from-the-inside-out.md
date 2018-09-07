---
title: Git 内幕（一）
date: 2018-09-07 17:30:10
tags: [git]
---

## 前言

git 是一种程序员几乎每天都会用到的工具，给我们代码管理带去了极大的方便。以往的 git 介绍，多是介绍git 的高级命令，如`git rebse`、`git cherry-picker`、`git bisect`等，少有看到剖析git 内部原理的。原因也很简单，即使对 git 的原理不甚了解，也并不会影响我们熟练使用 git。但是很多事我们不光要知其然，更要知其所以然，方能举一反三。

## 概念

在介绍 git 的原理之前，先介绍几个基本概念和会用到的命令，以便大家在阅读文章时能更加轻松地理解。

- BLOB (binary large object)：二进制大对象，是一个可以存储二进制文件的容器。
- three git status：
  1. Untracked：文件还未被`git add`，对应工作区（Working Directory）。
  2. Staged：`git add` 后所处的状态， 对应暂存区（Stage）。
  3. Committed：`git committe`后所处的状态，对应版本库（Commit History）。
- `git cat-file`：查看 git 对象的瑞士军刀，能转译二进制文件为可读文件。
- `git hash-object -w filename`：查看到 git 已存储的数据
- `hexdump -C filename`：查看二进制文件的十六进制编码

## .git 内幕

当你在一个新目录或已有目录内执行 `git init` 时，git 会创建一个 .git 目录，几乎所有 git 存储和操作的内容都位于该目录下。记得在刚接触 git 时，因为对 `stage`、`branch` 等概念不够了解，还做出过备份整个项目的蠢事。其实如果真要做备份，备份当前的 .git 文件即可。

下图为刚初始化的 .git 的目录结构（不同版本的 git 会有所不同），几乎所有的 git 操作和存储都位于该目录下。本次真要介绍四个核心文件或目录：HEAD 及 index 文件，objects 及 refs 目录。
<img src="http://img.souche.com/f2e/2b49ad6927521745890bea4b0313be3a.png"  style="height: 400px"/>


简而言之，git 从核心上来看不过是简单地存储键值对（key-value）。它允许插入任意类型的内容，并会返回一个键值，通过该键值可以在任何时候再取出该内容。

## git add

先抛出一个问题：[一个空的文件夹是否能添加到 git 项目中](https://git.wiki.kernel.org/index.php/GitFaq#Can_I_add_empty_directories.3F)？很多人可能都知道答案，但是为什么呐？通过对 `git add` 背后原理的解析，相信你可以得出一个确切的答案。

1. 初始化

```shell
$ mkdir alpha && cd alpha
$ git init
$ mkdir data
```

通过执行上面的命令，我们创建了一个名为 `alpha` 的文件夹，并且将其初始化为一个 git 项目，再新建一个 `data`  空文件夹。通过执行 `git add data` 命令希望把 `data` 空文件夹添加到暂存区中，发现执行后并没有发生任何变化，执行 `git status` 查看仓库的状态，抛出这样一段提示：

```shell
$ git status
On branch master
No commits yet
nothing to commit (create/copy files and use "git add" to track)
```

如果在 `data` 文件夹内再新建一个空文件夹又会如何呐，发现还是没有任何变化。这样就得出了开头那个问题的答案，空的文件夹是无法添加到 git 项目中的。再查看一下 `.git` 目录，也没有发生任何变化。

1. 添加文件夹到暂存区

```shell
$ echo 'a' > data/letter.txt
$ git status
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	data/

nothing added to commit but untracked files present (use "git add" to track)
```

在 `data` 目录下新建一个名为 `letter.text` 的文件，写入 `a`。发现仓库的状态发生了变化，但是 `.git` 目录并没有发生变化，说明在工作区的操作并不会产生 git  历史记录。
执行 `git add data` 命令，发现 `.git` 目录终于发生变化了。 分别多了 `index` 文件和 `objects/78/...`文件。
<img src="http://img.souche.com/f2e/60ea80780510852942ea35d5b24006e9.png"  style="height: 400px"/>


之前说过，git 的存储其实是以键值对的形式存在的。`index`是一个列表，每一行维护一个文件名到 BLOB 哈希值的映射。`objects` 目录下存放的就是 value，这个BLOB文件包含了`data/letter.txt` 文件压缩后的内容，并以内容的哈希值作为文件名。哈希是一段算法，它将给定内容转换为更小的，且能唯一确定原内容的值。
我曾经尝试直接打开这些文件，但是打开都是一串乱码。可以通过十六进制编码的方式打开这些文件，但是毕竟不是机器，想要看明白还是有一些难度的🤣。
<img src="http://img.souche.com/f2e/4cdf9a1851138b937978f231a0367bcd.png" />

<img src="http://img.souche.com/f2e/d6c97363f6810afc85c0575f14ad10bc.png" />


幸好 git 提供了一把瑞士军刀（`git cat-file`）给我们使用，可以将数据内容取回。
打印出 `data/letter.txt` 中的内容为 `a`。

```shell
$ git cat-file -p 78981922613b2afb6025042ff6bd878ac1994e85
a
```

至于 `git/index` 中的内容，可以通过 `git ls-file` 查看。

```shell
$ git ls-file --stage
100644 78981922613b2afb6025042ff6bd878ac1994e85 0	data/letter.txt
```

正如之前所说的，`index` 文件存放的是一个列表，记录了哈希值对应的文件。`7898` 这个 BLOB 文件存储的是 `data/letter.txt` 中的内容。`objects`目录下的结点是不可变的。这意味着它的内容可以编辑，但不能删除。添加的文件内容和创建的提交都保存在 `objects` 目录下。（注：`git prune` 删除所有不能被ref访问到的对象，执行此命令可能会丢失数据。）

## git commit

```shell
$ git commit -m 'a1'
[master (root-commit) b83b660] a1
 1 file changed, 1 insertion(+)
 create mode 100644 data/letter.txt
$ git status
On branch master
nothing to commit, working tree clean
```

执行 `git commit` 命令后，文件被保存到了版本库中，仓库处于 `clean`  的状态。再看看 `.git` 目录，发现多了 `logs` 目录和 `objects` 下的三个文件。
<img src="http://img.souche.com/f2e/3c70827c8b4cae05f478c198b226338b.png" style="height: 400px"/>



提交命令其实包含了三个步骤：

1. 创建提交版本对应文件的 tree 对象
   什么是 tree 对象，tree 对象可以存储文件名，同时也允许存储一组文件。一个单独的 tree 对象包含一条或多条 tree 记录，每一条记录含有一个指向 BLOB 或子 tree 对象的哈希值。
   创建新提交后，对应data目录的tree对象如下：记录了 `data/letter.txt` 文件的所有信息，我们可以使用这些信息来恢复 `data/letter.txt` 文件。空格分隔的第一部分表示该文件的权限，表明是一个普通文件；第二部分表示该记录对应的是一个 BLOB 对象，第三部分是该BLOB 的哈希值，第四部分记录了文件名。

```shell
$ git cat-file -p e908cfc6e086c91a073c55a6882adebfc9c4520c

100644 blob 78981922613b2afb6025042ff6bd878ac1994e85	letter.txt
```

用图示表式即为：`data`目录对应的 tree 对象指向对应`data/letter.txt`
<img src="http://img.souche.com/f2e/a8ad7fae09f810a882177f8654cb6084.png" style="height: 200px"/>

那么问题来了，data 并非根目录，文件的指向肯定是从根目录开始。

```shell
$ git cat-file -p master^{tree}
040000 tree e908cfc6e086c91a073c55a6882adebfc9c4520c	data

$ git cat-file -p 9745002f161a1be75bf65f869ab16743da2a6fda

040000 tree e908cfc6e086c91a073c55a6882adebfc9c4520c	data

```

`master^{tree}` 表示 master （当前）分支上最新提交指向的 tree 对象。从给出的结果中可以看到根目录（root）指向了 `data` 目录，图示如下：
<img src="http://img.souche.com/f2e/b537540955ff6466f15529bb846326d3.png" style="height: 300px"/>

1. 创建一个提交对象
   我们在提交时输入了 `commit message` 等信息，这些信息自然也存放在`objects` 目录下：

```
$ git cat-file -p b83b66096fb5b5225ec0c5741f45d334ceb94046

tree 9745002f161a1be75bf65f869ab16743da2a6fda
author Bill Qiu <fanglaiq@gmail.com> 1536220281 +0800
committer Bill Qiu <fanglaiq@gmail.com> 1536220281 +0800
a1
```

第一行指向一个tree对象。通过这里的哈希值，我们可以找到对应工作区根目录（即 alpha 目录）的 tree 对象，最后一行是提交信息。
<img src="http://img.souche.com/f2e/b537540955ff6466f15529bb846326d3.png" style="height: 400px"/>

1. 将当前分支指向新提交
   到了这里还存在最后一个问题，分支。 git 怎么知道现在的提交应该指向哪个分支？答案在 `HEAD` 文件中：`ref: refs/heads/master`。HEAD 指向 master，master 就是我们当前分支。因为是第一个提交，代表master引用的文件还不存在。git 会自动创建 `.git/refs/heads/master` ，并写入提交对象的哈希值：`b83b66096fb5b5225ec0c5741f45d334ceb94046`。
   <img src="http://img.souche.com/f2e/bea53a7f80fc1acb2663a6262768a9fb.png" style="height: 400px"/>
   

最后，所有 BLOB 文件所存储的信息都通过 tree 对象串联了起来，就好比 `key-value` 的形式。

再来回答下开头提出的问题吧，一个空的文件夹是否能添加到 git 项目中？
答：不可以。因为 git 使用的索引机制，是以文件为最小单位存储内容，跟踪变化的。
那么怎么做才可以使这个文件夹存在呐？通常的做法是在里面新建一个名为 `.gitkeep` 的文件。



## 参考链接

- [Git - Git 内部原理](https://git-scm.com/book/zh/v1/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86)
- [Git from the inside out](https://codewords.recurse.com/issues/two/git-from-the-inside-out)
- [Git FAQ - Git SCM Wiki](https://git.wiki.kernel.org/index.php/GitFaq#Can_I_add_empty_directories.3F)

