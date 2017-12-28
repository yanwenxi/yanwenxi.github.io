---
title: Angular 路由实现原理
date: 2016-12-16 13:25:38
tags: angular、router
---
   路由 (route) ，几乎所有的 MVC(VM) 框架都应该具有的特性，因为它是前端构建单页面应用 (SPA) 必不可少的组成部分。那么，对于 angular 而言，它自然也有 内置 的路由模块：叫做 ngRoute 。不过，大家很少用它，因为它的功能太有限，往往不能满足开发需求！！于是，一个基于 ngRoute 开发的 第三方路由模块 ，叫做 ui.router ，用它的很多哦！

#### ngRoute vs ui.router
1. ngRoute 提供的服务和指令如下：
 - $routeProvider(服务提供者) --------- 对应于下面的urlRouterProvider和stateProvider
 - $route(服务) --------- 对应于下面的urlRouter和state
 - $routeParams(服务) --------- 对应于下面的stateParams
 - ng-view(指令) --------- 对应于下面的ui-view
 
2.  ui.router 提供的服务和指令如下：
 - $urlRouterProvider(服务提供者) --------- 用来配置路由重定向
 - $urlRouter(服务)
 - $stateProvider(服务提供者) --------- 用来配置路由
 - $state(服务) --------- 用来显示当前路由状态信息，以及一些路由方法（如：跳转）
 - $stateParams(服务) --------- 用来存储路由匹配时的参数
 - ui-view(指令) --------- 路由模板渲染，对应的dom相关联
 - ui-sref(指令)---------链接到特定状态 例:<a ui-sref="home">Home</a>
 - .......
 
 注：服务提供者：用来提供服务实例和配置服务
 两者不同的地方在于功能点的实现和增强！！！
 
 ##### 列举两个最重要的方面来说
 1. 多视图
 2. 嵌套视图
 
#### 多视图：页面可以显示多个动态变化的不同区块。
 
  比如：页面一个区块用来显示页面状态，另一个区块用来显示页面主内容，当路由切换时，页面状态跟着变化，对应的页面主内容也跟着变化。
 ng-route对应代码：
 ```
 html
 <div ng-view>区块1</div>
 <div ng-view>区块2</div>

 js
 
 $routeProvider
     .when('/', {
     template: 'hello world'
 });
 ```
 我们在html中利用ng-view指令定义了两个区块，于是两个div中显示了相同的内容，这很合乎情理，但却不是我们想要的，但是又不能为力，因为，在ngRoute中：视图没有名字进行唯一标志，所以它们被同等的处理。路由配置只有一个模板，无法配置多个。
 
 针对上述两个问题，我们尝试用 ui.router 来做：
 ```
 html
 
   <div ui-view></div>
   <div ui-view="status"></div>
 js
 
 $stateProvider
 	.state('home', {
 		url: '/',
 		views: {
 			'': {
 				template: 'hello world'
 			},
 			'status': {
 				template: 'home page'
 			}
 		}
 	});
 ```
 这次，结果是我们想要的，两个区块，分别显示了不同的内容，原因在于，在ui.router中：
 可以给视图命名，如：ui-view="status"。
 可以在路由配置中根据视图名字(如：status)，配置不同的模板（其实还有controller等）。
 注 ：视图名是一个字符串，不可以包含 @;
 
#### 嵌套视图
 
 嵌套视图：页面某个动态变化区块中，嵌套着另一个可以动态变化的区块。
 
 比如：页面一个主区块显示主内容，主内容中的部分内容要求根据路由变化而变化，这时就需要另一个动态变化的区块嵌套在主区块中。
 
 其实，嵌套视图，在html中的最终表现就像这样：
 ```
 <div ng-view>
 I am parent
 <div ng-view>I am child</div>
 </div>
 转成javascript，我们会在程序里这样写：
 
 $routeProvider
     .when('/', {
     template: 'I am parent <div ng-view>I am child</div>'
 });
 ```
   倘若，你真的用 ngRoute 这样写，你会发现浏览器崩溃了，因为在ng-view指令link的过程中，代码会无限递归下去。那么造成这种现象的最根本原因： 路由没有明确的父子层级关系！
 
   看看 ui.router 是如何解决这一问题的？
 ```
 $stateProvider
 	.state('parent', {
 		abstract: true,
 		url: '/',
 		template: 'I am parent <div ui-view></div>'
 	})
 	.state('parent.child', {
 		url: '',
 		template: 'I am child'
 	});
 ```
   巧妙地，通过 parent 与 parent.child 来确定路由的 父子关系 ，从而解决无限递归问题。
 另外子路由的模板最终也将被插入到父路由模板的div[ui-view]中去，从而达到视图嵌套的效果。
  注：ui.router是基于state(状态)的，而不是url
 
#### 前端路由的基本原理
  1. 哈希 #
  2. HTML5 中新的 history API
  3. 路由的核心是给应用定义 "状态"
  4. 使用路由机制会影响到应用的整体编码方式(需预先定义好状态)
  5. 考虑兼容性问题与 "优雅降级"
  
#### ui.router工作原理
  
  路由，大致可以理解为：一个 查找匹配 的过程。
  
  对于前端 MVC(VM) 而言，就是将 hash值 (#xxx)与一系列的 路由规则 进行查找匹配，匹配出一个符合条件的规则，然后根据这个规则，进行数据的获取，以及页面的渲染。
  
#### ui-router补充
1. $state.includes 返回 true / false
    以上方法为查看当前状态是否在某父状态内，比如 $state.includes('contacts') 返回 true / false
```
<!-- 包含在 /contacts 状态内部，即其作为 parant state -->
<li ng-class="{active: $state.includes('contacts')}">
    <a ui-serif="contacts.list">Contacts</a>
</li>
```
2. ui-sref-active 查看当前激活状态并设置 Class
```
<li ui-sref-active="active"><a ui-sref="about">About</a></li>
```

3. 包含模块
```
angular.module('uiRouter', ['ui.router']);
```

4. 方便获得当前状态的方法，绑到根作用域
```
app.run(['$rootScope', '$state', '$stateParams',
    function($rootScope, $state, $stateParams) {
        $rootScope.$state = $state;
        $rootScope.$stateParams = $stateParams;
    }
]);
```
5. 路由重定向 $urlRouterProvider
```
app.config(['$stateProvider', '$urlRouterProvider',
	function($stateProvider, $urlRouterProvider) {
		$urlRouterProvider
			// 错误的路由重定向
			.when('/c?id', '/contacts/:id')
			.when('/user/:id', '/contacts/:id')
			.otherwise('/');
	}
]);
```
6. 状态配置
```
$stateProvider.state('about',{})
```

