---
title: NPM包本地开发调试
date: 2020-10-10 11:25:24
tags: npm
---

### link:
开发阶段，不用频繁的升级package的版本发布，进行测试：

只需要在package根目录执行：npm link

然后在依赖此package的项目仓库的根目录执行： npm link package发布名称

这样在package仓库中开发编译，项目就能引用到最新的资源。

### 解除link:
在项目仓库根目录执行：npm unlink --no-save  package发布名称

在package根目录执行：npm unlink

<p color="red">注意： 解除的顺序很重要！</p>