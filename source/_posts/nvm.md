---
title: Mac下安装NVM及常用命令
date: 2020-10-22 15:49:03
tags: [NVM, Node]
---
1. #### 理解NVM、Node、NPM
	* 什么是NVM？
	NVM: Node.js Version Manager，用来管理 node 的版本。

	* 什么是 Node.js?
	Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 Node.js使用了一个事件驱动、非阻塞式I/O的模型（ Node.js的特性），使其轻量级又高效。

	* NPM
	Node Package Manager，node包管理器

2. #### Mac下安装NVM
	点击跳转(nvm项目的[github地址](https://github.com/creationix/nvm)) ，找到install script的地方，然后复制，在终端粘贴运行。

	我此时连接 ` curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash `

	安装成功后，输入 nvm --version输出版本号即为安装成功。

3. #### snvm 常用命令
	* nvm install stable  安装最新稳定版 node

	* nvm install <version>  安装指定版本，如：安装v4.4.0，nvm install v4.4.0

	* nvm uninstall <version>  删除已安装的指定版本，语法与install类似

	* nvm use <version>  切换使用指定的版本node

	* nvm ls  列出所有安装的版本

	* nvm alias default <version>  如： nvm alias default v11.1.0
