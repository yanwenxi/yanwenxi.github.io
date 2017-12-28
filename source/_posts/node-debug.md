---
title: debug模块介绍
date: 2017-01-05 10:44:37
tags: node debug
---
一般在nodejs需要进行调试的时候，可以使用console.log()方法来将调试信息输出到控制台，当发布到生产环境的时候，需要将这些调试信息都注释掉，为了方便切换而不需要改动程序代码，可以使用nodejs的debug模块;
gsb常用调试语句如下：
```
set DEBUG=gsb:* && set CMS_HOST=w2&& node app.js --harmony
```
debug调试打印的是开发者自己在 控制台 打印的信息，它的目的是要代替 console.log()。
特点：1.可以设置打印的信息来自哪个哪个模块：控制台输出的蓝色字体就是表示来自app模块（自己写的模块名）。 2.在发布到外网的情况下，可以用发布模式，这时 debug里打印信息将不会在显示。

```
var debug = require('debug')('myapp:main');
debug('现在的时间是 %s' , new Date());
```
如果是开发环境，只需要设置环境变量
```
D:\nodej\workspace\spider\set debug=myapp:*
```
此后再控制台执行node app.js即可输出调试信息了
如果需要关闭调试信息
```
D:\nodej\workspace\spider\set debug=null
```
具体的使用方法可以参考[debug模块](http://npmjs.org/package/debug)的主页 http://npmjs.org/package/debug

