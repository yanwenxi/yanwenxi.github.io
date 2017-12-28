---
title: Angular路由之ui-router
date: 2015-12-05 23:50:58
tags: angular、ui-router
---
#### ui-router
   AngularUI库提供的最有用的库之一便是ui-router。它是一个路由框架，允许你通过状态机组织接口，而不是简单的URL路由。ng-router能办的事，ui-router都能做到，所以说会了ui-router，你就可以忘记ng-router,以免浪费脑细胞。哈哈
   安装：
   ```
   $ bower install angular-ui-router --save
   ```
   你还要确保在视图中链接这个库：
   ```
   <scripttype="text/javascript"
   src="app/bower_components/angular-ui-router/release/angular-ui-router.js"></script>
   ```
   同时还需要将ui.router作为依赖注入到你的应用中：
   ```
   angular.module('myApp', ['ui.router']);
   ```
   不同于内置的ngRoute服务，由于ui-router基于状态工作，而不是简单的url，因此你可以将它嵌套在视图中。
   在处理uiRoute服务时我们不再使用ng-view，而改为使用`ui-view`指令。
   在ui-router内处理路由和状态时，我们主要关心的是应用程序处在哪个状态以及Web应用当前处在哪个路由位置。
#### 定义路由
   为了定义路由，你可以使用.config方法，和常见的方式一样，但不是将路由设置在$routeProvider上，而是将状态设置在$stateProvider上。
   ```
   .config(function($stateProvider,$urlRouterProvider) {
   $stateProvider
   .state('start', {
   url: '/start',
   templateUrl: 'partials/start.html'
   })
   });
```
这一步给状态配置对象分配了一个名为start的状态。这个状态配置对象，或者说这个stateConfig也有一些与路由配置对象相似的选项，让你能够配置应用程序的状态。
1. template、 templateUrl、 templateProvider
在每个视图上设置模板的方式有三种。
 template：一个HTML内容字符串或者返回HTML的函数。
 templateUrl：一个模板URL路径字符串或者是返回URL路径字符串的函数。
 templateProvider：一个返回HTML内容字符串的函数。
例如：
$stateProvider.state('home',{
