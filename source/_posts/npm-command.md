---
title: npm常用命令介绍
date: 2021-04-29 13:49:03
tags: [npm, 命令]
---
#### -S
即--save（保存）
包名会被注册在package.json的dependencies里面，在生产环境下这个包的依赖依然存在

#### -D
即--save-dev（生产）
包名会被注册在package.json的devDependencies里面，仅在开发环境下存在的包用-D，如babel，sass-loader这些解析器

#### -g
全局安装

#### -–unsafe-perm
snpm 出于安全考虑不支持以 root 用户运行，即使你用 root 用户身份运行了，npm 会自动转成一个叫 nobody 的用户来运行，而这个用户几乎没有任何权限。这样的话如果脚本里有一些需要权限的操作，比如写文件（尤其是写 /root/.node-gyp），就会崩掉。

为了避免这种情况，要么按照 npm 的规矩来，专门建一个用于运行 npm 的高权限用户；要么加 --unsafe-perm 参数，这样就不会切换到 nobody 上，运行时是哪个用户就是哪个用户，即使是 root。
```
	npm install -–unsafe-perm
```

#### --force 或 -f
一个模块不管是否安装过，npm都要强制重新安装，可以使用-f或--force参数
```
	npm install <packageName> --force
```

#### update
update会先到远程仓库查询最新版本，然后查询本地版本。如果本地版本不存在，或者远程版本较新，就会安装。
```
	npm update <packageName>
```

#### view
查看注册表信息
```
	npm view <packageName> version //查询模块最新版本
	npm view <packageName> versions //查询模块所有版本
```