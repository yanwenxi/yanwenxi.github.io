---
title: 域名服务器及ngrok环境配置。
date: 2017-01-10 16:43:14
tags: node wechat
---
域名最好买一个，万一你先买了个牛比的域名，到时候还能卖不少钞票，哈哈，如果你不是专门炒域名的，估计比中彩票机率还小。服务器最好是用vpn搭建一个，对新手来说有一定的难度，所以推荐使用下面的方法。
<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/wechat1.png-public)
    ![](http://oi28tswuq.bkt.clouddn.com/images/wechat2.png-public)
    ![](http://oi28tswuq.bkt.clouddn.com/images/wechat3.png-public)
</div>
ngrok并不稳定，推荐使用一个网站：www.tunnel.mobi.
1.mac系统自带python web服务,通过python在控制台开启一个简单的web服务器，命令如下：
```
python m SimpleHTTPSwrver 8080,8080为端口号。
```
当然也可以使用nginx，apache等等开启一个本地web服务，只要能开启一个特定端口号的服务即可。

2.按照mobi官方文档来，开启ngrok这个工具。
```
./ngrok -config ngrok.cfg -subdomain webywx-wechat 8080//ywx-wechat为子域名
```
开启成功之后，会得到两个链接，一个http的,一个https的，例：
```
http://webywx-wechat.tunnel.mobi->127.0.0.1:8080
https://webywx-wechat.tunnel.mobi->127.0.0.1:8080
```
注：当然端口号建议使用1234,4321等小数字的，有人碰到过使用大数字而导致代理失效

3.复制链接能成功打开即说明成功了。不过有时候此网站使用的人也会很多，流量太大服务器直接挂了，或者作者把服务器关了，此时建议使用nodejs的第三方模块：localtunnel服务

#### 建议使用localtunnel服务（比较方便）：方法如下：
安装：npm install -g localtunnel
安装成之后可以通过localtunnel的缩写lt来开启一个端口：lt --prot 8080
运行之后它会自动生成一个随机的二级域名。复制到浏览器能打开就成功了。
缺点就是不能指定特定的域名，它会随机生成。关闭服务重启url会变，所以还需要重新配置公众号后台的url。
