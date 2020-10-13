---
title: nrm安装与配置
date: 2020-10-10 11:49:03
tags: [npm, nrm]
---
1. 简介
nrm(npm registry manager )是npm的镜像源管理工具，有时候国外资源太慢，使用这个就可以快速地在 npm 源间切换

2. 安装nrm
在命令行执行命令，npm install -g nrm，全局安装nrm。

3. 使用
执行命令nrm ls查看可选的源。
```
nrm ls                                                                                                                                   

*npm ---- https://registry.npmjs.org/
cnpm --- http://r.cnpmjs.org/
taobao - http://registry.npm.taobao.org/
eu ----- http://registry.npmjs.eu/
au ----- http://registry.npmjs.org.au/
sl ----- http://npm.strongloop.com/
nj ----- https://registry.nodejitsu.com/
```
其中，带*的是当前使用的源，上面的输出表明当前源是官方源。

4. 切换
如果要切换到taobao源，执行命令`nrm use taobao`。

5. 增加
你可以增加定制的源，特别适用于添加企业内部的私有源，执行命令 nrm add <registry> <url>，其中reigstry为源名，url为源的路径。

    `nrm add registry http://registry.npm.frp.trmap.cn/`

6. 删除
执行命令`nrm del <registry>`删除对应的源。

7. 测试速度
你还可以通过 nrm test 测试相应源的响应时间。

    `nrm test npm`