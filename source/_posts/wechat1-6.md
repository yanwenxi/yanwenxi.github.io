---
title: 写段代码实现加密认证逻辑
date: 2017-01-10 17:07:14
tags: node wechat
---
为什么使用koa而不是express,说通俗点就是装比。当然不只是装比，其实用koa框架，代码可以更精简，更易懂，对于反反复复的异步交互更适合用这个框架实现。
```
"use strict"
// npm install koa sha1
var Koa=require('koa');
var sha1=require("sha1");
var config={
	wechat:{
		appID: '',//wechat管理页面的appid
		appsecret: '',//wechat管理页面的appSecret
		token: 'webywx',//wechat管理页面的token
	//这三个参数非常重要，不要轻易暴露给外网。
	}
	
};
var app= new Koa();
app.use(function * (next) {//koa的中间件只能写成generator函数形式。
	//console.log(this.query);
	//浏览器输入localhost:1234？a=1就可以在浏览器控制台看到查询参数了。{a:1}
	
	var token=config.wechat.token;
	var signature=this.query.signature;
	var nonce =this.query.nonce;
	var timestamp=this.query.timestamp;
	var echostr=this.query.echostr;

	//字典排序
	var str=[token,timestamp,nonce].sort().join('');
	var sha=sha1(str);
	if(sha===signature){
		this.body=echostr+'';
	}else{
		this.body='wrong';
	}
});
app.listen(1234);
console.log('Listening:1234');

```
注：版本必须是11以上才能使用koa框架。可以用node -v 查看node的版本号。//v0.12.4  这里显示的是12（0后面的），所以可以用。
运行：node --harmony app.js  //以和谐模式启动app.js,保证node支持es6新特性：如generator等.  

 填写完wechat管理页面的url和token，点击提交就可以进行公众号认证了。若点击后出现配置失败,可能出现的错误如下：虽然把本地的服务跑起来了（node --harmony app.js用此跑起来的），但是并没有把这个服务通过ngrok暴露到外网去，所以在点击提交的时候，微信服务器它并没有收到post请求，因为它没有连接到我们的本地。
解决办法：把ngrok这个代理跑起来。用./ngrok -config ngrok.cfg -subdomain url中的子域名  app.js里监听的端口号

当用./ngrok -config ngrok.cfg -subdomain url中的子域名  app.js里监听的端口号 这种方法开启时，若发现连接失败，可以使用另外一种方法，localhosttunnel服务，用这种方法时，记得把之前配置的url换成自动生成的这个即可。