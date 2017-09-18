---
title: 如何使用git tag
date: 2016-10-31 16:02:57
tags: [git, tag]
---

## 定义

> 对某一时间点上的版本打上标签。

<!-- more -->

## 用法

1. 列出所有标签 `git tag`

   ```shell
   $ git tag
   v0.1
   v1.3
   ```

   显示的标签按字母顺序排列，所以标签的先后并不表示重要程度的轻重。

2. 新建标签

   打标签的方式分2种，轻量级的（lightweight）和含附注的（annotated）。

   - 轻量级：直接给出标签名字即可

     ```shell
     $ git tag v0.1
     $ git tag
     v0.1
     ```

   - 含附注的标签

     `-a`(`annotated` 的首字母)

     `-m`(指定了对应的标签说明)

     ```shell
     $ git tag -a v1.4 -m 'my version 1.4'
     $ git tag
     v1.4
     ```

3. 查看相应标签的版本信息 `git show`

   上面我们打了一个v1.4版本的节点，就可以查看这个打点节点的信息。

   ```shell
   $ git show v1.4
   tag v1.4
   Tagger: Scott Chacon <schacon@gee-mail.com>
   Date:   Mon Feb 9 14:45:11 2009 -0800

   my version 1.4

   commit 15027957951b64cf874c3557a0f3547bd83b3ff6
   Merge: 4a447f7... a6b4c97...
   Author: Scott Chacon <schacon@gee-mail.com>
   Date:   Sun Feb 8 19:02:46 2009 -0800

       Merge branch 'experiment'
   ```

   ​

4. 分享标签 `git push`

   默认情况下，`git push` 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。其命令格式如同推送分支，运行 `git push origin [tagname]` 即可：

   ```shell
   $ git push origin v1.5
   ```

   如果要一次推送所有本地新增的标签上去，可以使用 `--tags` 选项：

   ```shell
   $ git push origin --tags
   ```

   一般来说，代码有较大的变化或者有重要功能更新的时候，才会将标签共享。

5. 删除本地标签`git tag -d [tagname]`

   删除远端标签`git push origin :[tagname]`

6. 标签命名

   v<大版本号>-<小版本号>-<补丁修复>

   `v`代表version，当有一个BUG修复后，增加补丁修复号。

   当有功能更新，增加小版本号。

   当有多个功能更新，增加大版本号。

## 使用场景

在这里，我们只对master打标签。当版本处于稳定后，就对master打上一个标签。方便自己也方便他人以后修改阅读代码。