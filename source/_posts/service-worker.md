---
title: service worker离线缓存
date: 2020-10-12 14:49:03
tags: [serviceWorker, 缓存]
---
#### service worker介绍
service worker是浏览器的一个高级特性，本质是一个web worker，是独立于网页运行的脚本。 web worker这个api被造出来时，就是为了解放主线程。因为，浏览器中的JavaScript都是运行在单一个线程上，随着web业务变得越来越复杂，js中耗时间、耗资源的运算过程则会导致各种程度的性能问题。 而web worker由于独立于主线程，则可以将一些复杂的逻辑交由它来去做，完成后再通过postMessage的方法告诉主线程。 service worker则是web worker的升级版本，相较于后者，前者拥有了持久离线缓存的能力。

sw是由事件驱动的,具有生命周期，可以拦截处理页面的所有网络请求(fetch)，可以访问cache和indexDB，支持推送，并且可以让开发者自己控制管理缓存的内容以及版本，为离线弱网环境下的 web 的运行提供了可能。

#### service worker的特点
* 独立于主线程、在后台运行的脚本
* 被install后就永远存在，除非被手动卸载
* 可编程拦截全站请求和返回，缓存文件。可以通过fetch这个api，来拦截网络和处理网络请求，再配合cacheStorage来实现web页面的缓存管理以及与前端postMessage通信。
* 不能直接操纵dom：因为sw是个独立于网页运行的脚本，所以在它的运行环境里，不能访问窗口的window以及dom。
* 必须是https的协议才能使用。不过在本地调试时，在http://localhost 和http://127.0.0.1 下可以跑起来的。
* 异步实现，sw大量使用promise，无法使用XHR和localStorage
* 可以接收server端消息push

#### 生命周期
+ **(安装)installing**：发生在service worker注册之后，表示开始安装。在这个过程会触发install事件回调指定一些静态资源进行离线缓存。

+ **(安装后)installed**：完成了安装，进入了waiting状态，等待其他的Service worker被关闭（在install的事件回调中，可以调用skipWaiting方法来跳过waiting这个阶段）

+ **(激活)activating**： 该状态下没有被其他的 Service Worker 控制的客户端，允许当前的 worker 完成安装，并且清除了其他的 worker 以及关联缓存的旧缓存资源，等待新的 Service Worker 线程被激活。

+ **(激活后)activated**： 在这个状态会处理activate事件回调，并提供处理功能性事件：fetch、sync、push。（在acitive的事件回调中，可以调用self.clients.claim()）

+ **(废弃)redundant**：废弃状态，这个状态表示一个sw的使命周期结束

#### webpack中的应用
webpack中可以通过使用workbox-webpack-plugin、sw-register-webpack-plugin、offline-plugin等插件使用service worker。

#### 简单实现
```
	/**   注册    **/
	//在页面代码里面监听onload事件，使用sw的配置文件注册一个service worker
	// 当前环境是否支持service woker
	if('serviceWorker' in navigator) {
		// 尽量避免页面打开时立刻加载，挤占主线程的带宽、cpu等资源
		window.addEventListener('load', function () {
			/** sw文件的作用域不同，监听fetch请求也是不一样。假设sw文件放在/sw/sw.js路径的话，
			*	那么sw就只能监听/sw/*下面的请求
			*/
			navigator.serviceWorker.register('.js')
			.then(function (registration) {
				// 注册成功
				console.log('registration successful with scope: ', registration.scope);
			})
			.catch(function (err) {
				// 注册失败
				console.log('ServiceWorker registration failed: ', err);
			});
		});
	}
```

```
	/**   安装和激活    **/
	const CACHE_NAME = "fed-cache";
	// 安装服务线程
	this.addEventListener("install", function(event) {
		console.log("install service worker");
		// 创建和打开一个缓存库
		caches.open(CACHE_NAME);
		// 需要缓存的资源，
		let cacheResources = ["index.css", "common.js"];
		event.waitUntil(
			// 请求资源并添加到缓存里面去，有一个失败就会安装失败
			caches.open(CACHE_NAME)
			.then(cache => {
				cache.addAll(cacheResources);
			})
			.then(function() {
				// 注册成功跳过等待，酌情处理
				return self.skipWaiting()
			})
		);
	});
	
	
	// 激活回调,删除旧版本的缓存
	this.addEventListener("activate", function(event) {
		console.log("service worker is active");
		event.waitUntil(
			caches
			.keys()
			.then(cacheNames => {
				return cacheNames.filter(cacheName => cacheStorageKey !== cacheName);
			})
			.then(cachesToDelete => {
				return Promise.all(
					cachesToDelete.map(cacheToDelete => {
						return caches.delete(cacheToDelete);
					})
				);
			})
			.then(() => {
				console.log("Clients claims.");
				// 立即接管所有页面，酌情处理
				// 会导致新的sw接管旧的页面，同时旧版本的缓存已被清空
				self.clients.claim();
			})
		);
	});
	
	
	// 发起请求时去根据uri去匹配缓存，无法命中缓存则发起请求
	self.addEventListener("fetch", function(e) {
	e.respondWith(
		caches.match(e.request).then(function(response) {
			// 可以在这里对请求进行一系列的判断，比如GET/POST请求，图片/HTML资源，URL等
			return response || fetch(e.request);
		})
	);
	});
	
	// 监控push消息
	self.addEventListener("message", event => {
	if (event.data === "skipWaiting") {
		console.log("Skip waiting!");
		self.skipWaiting();
	}
	});
```