---
title: 让AngularJS指令符合html5规范
date: 2016-12-12 22:04:54
tags: angular、html5
---
　　AngularJS,是Google开源的一款JavaScript MVVM框架，弥补了HTML在构建应用方面的不足，其通过使用指令（directives）结构来扩展HTML的属性，使开发者可以使用HTML来声明动态内容，从而使得Web开发和测试工作变得更加容易。

　　在学习中无意间看到有人说自定义指令如果想符合HTML5规范，可以加上data-*或者x-*前缀，对指令本身不影响，我顺便也试了下默认指令，发现ng-app、ng-model、ng-bind这类默认指令也可以直接加上data-*的前缀，而且完全不用配置，不影响功能。

　　所以，为了遵循HTML5规范，我们也可以使用data-ng-app、data-ng-model、data-ng-bind这样的属性来使用AngularJS的默认指令了。

　　最后来验证一下：原程序里面是这样写的
```
var ngAttrPrefixes = ['ng-', 'data-ng-', 'ng:', 'x-ng-'];
```
　　所以说，ng-、data-ng-、ng:、x-ng-，都是可以生效的。
