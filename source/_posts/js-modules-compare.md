---
title: JS模块规范对比：AMD，CMD，CommonJS
date: 2016-12-13 16:46:32
tags: js模块化、AMD、CMD、CommonJs
---
AMD,CMD,CommonJS是目前最常用的三种模块化书写规范。
### CommonJS （CommonJs Modules规范的简写）
*NodeJS就采用了CommonJS*
```
var clock = require('clock');
clock.start();
```
　　第二行clock.start()，在第一行require('clock')之后运行，因此必须等clock.js加载完成。也就是说，如果加载时间很长，整个应用就会停在那里等。

　　这对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于"假死"状态。

　　因此，浏览器端的模块，不能采用"同步加载"（synchronou），只能采用"异步加载"（asynchronous）。这就是AMD规范CMD规范诞生的背景。

　　CommonJs是同步加载模块，这种同步机制应用到浏览器中就会出现问题了，例如加载速度啥的（浏览器同步加载模块会导致性能、可用性、调试和跨域访问等问题），Commonjs是一个更偏向于服务器端的规范。当然你也可以把它用在浏览器里边。Node.js采用了这个规范。 根据CommonJS规范，一个单独的文件就是一个模块。加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象。
### AMD
　　AMD，即 (Asynchronous Module Definition)，这种规范是异步的加载模块，requireJs应用了这一规范。先定义所有依赖，然后在加载完成后的回调函数中执行：
```
require([module], callback);
```
用AMD写上一个模块：
```
require(['clock'],function(clock){
  clock.start();
});
```
　　AMD虽然实现了异步加载，但是开始就把所有依赖写出来是不符合书写的逻辑顺序的，能不能像commonJS那样用的时候再require，而且还支持异步加载后再执行呢？
### CMD
　　CMD (Common Module Definition), 是seajs推崇的规范，CMD则是依赖就近，用的时候再require。它写起来是这样的：
```
define(function(require, exports, module) {
   var clock = require('clock');
   clock.start();
});
```
　　AMD和CMD最大的区别是对依赖模块的执行时机处理不同，而不是加载的时机或者方式不同，二者皆为异步加载模块。
　　AMD依赖前置，js可以方便知道依赖模块是谁，立即加载；而CMD就近依赖，需要使用把模块变为字符串解析一遍才知道依赖了那些模块，这也是很多人诟病CMD的一点，牺牲性能来带来开发的便利性，实际上解析模块用的时间短到可以忽略。
　　对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。CMD 推崇依赖就近，AMD 推崇依赖前置。CMD 推崇 as lazy as possible.

