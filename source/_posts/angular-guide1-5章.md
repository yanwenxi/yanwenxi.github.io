---
title: Angular权威指南学习（第1到5章）模块、控制器及一些基础
date: 2016-05-04 11:35:12
tags: angular、module、controller
---
#### angular在现代Web应用中经常要用到的一系列高级功能，例如：
1.解耦应用逻辑、数据模型和视图；2. Ajax服务；3. 依赖注入；4. 浏览历史（使书签和前进、后退按钮能够像在普通Web应用中一样工作）；5. 测试；
#### ip地址的类型
目前有IPv4和IPv6两种主要的IP地址类型,普遍使用的是IPv4地址，例如192.168.0.199这种形式，而IPv6地址是IPv62001:0db8:0000:0000:0000:ff00:0042:8329这种形式的。
#### ng-model
当AngularJS认为某个值可能发生变化时，它会运行自己的事件循环来检查这个值是否变“脏”。如果该值从上次事件循环运行之后发生了变化，则该值被认为是“脏”值。这也是Angular可以跟踪和响应应用变化的方式。这个事件循环会调用$digest()循环,这个过程被称作脏检查（dirty checking).

#### ng-app
 `ng-app`属性声明所有被其包含的内容都属于这个AngularJS应用，这也是我们可以在Web应用中嵌套AngularJS应用的原因。只有被具有`ng-app`属性的DOM元素包含的元素才会受AngularJS影响.

#### 使用module的好处：
 1.保持全局命名空间的清洁；2.编写测试代码更容易，并能保持其清洁，以便更容易找到互相隔离的功能；3.易于在不同应用间复用代码；4.使应用能够以任意顺序加载代码的各个部分。
#### 声明模块
AngularJS允许我们使用angular.module()方法来声明模块，这个方法能够接受两个参数，
第一个是模块的名称，第二个是依赖列表，也就是可以被注入到模块中的对象列表。
```
angular.module('myApp', []);
```
注：这个方法相当于AngularJS模块的setter方法，是用来定义模块的。
#### 引用模块
调用这个方法时如果只传递一个参数，就可以用它来引用模块。例如，可以通过以下代码来
引用myApp模块：`angular.module('myApp')`
注：这个方法相当于AngularJS模块的getter方法，用来获取对模块的引用。
#### 作用域
作用域scope是构成AngularJS应用的核心基础，作用域是视图和控制器之间的胶水。$scope对象是定义应用业务逻辑、控制器方法和视图属性的地方。$scope可以进行嵌套，也就是说我们可以引用父级$scope中的属性。
#### rootScope
AngularJS启动并生成视图时，会将根ng-app元素同$rootScope进行绑定。 $rootScope是所有$scope对象的最上层,$scope对象在AngularJS中充当数据模型，但与传统的数据模型不一样， $scope并不负责处理和操作数据，它只是视图和HTML之间的桥梁，它是视图和控制器之间的胶水。
```
angular.module('myApp', [])
.run(function($rootScope) {
$rootScope.name = "World";
});
```
#### 可以在AngularJS应用的模板中使用多种标记
 1.指令：将DOM元素增强为可复用的DOM组件的属性或元素。2.值绑定：模板语法\{ \{
 \} \}可以将表达式绑定到视图上。3.过滤器：可以在视图中使用的函数，用来进行格式化。 4.表单控件：用来检验用户输入的控件
#### ng-controller
ng-controller指令为这个DOM元素创建了一个新的$scope对象，并将它嵌套在$rootScope中。
```
angular.module("myApp", [])
.controller('MyController',
function($scope) {
$scope.name = "ywx";
});
```
#### $scope 的生命周期
$scope对象的生命周期处理有四个不同阶段。
1. 创建
在创建控制器或指令时， AngularJS会用$injector创建一个新的作用域，并在这个新建的控
制器或指令运行时将作用域传递进去。
2. 链接
当Angular开始运行时，所有的$scope对象都会附加或者链接到视图中。所有创建$scope对
象的函数也会将自身附加到视图中。这些作用域将会注册当Angular应用上下文中发生变化时需
要运行的函数。
这些函数被称为$watch函数， Angular通过这些函数获知何时启动事件循环。
3. 更新
当事件循环运行时，它通常执行在顶层$scope对象上（被称作$rootScope），每个子作用域
都执行自己的脏值检测。每个监控函数都会检查变化。如果检测到任意变化， $scope对象就会触
发指定的回调函数。
4. 销毁
当一个$scope在视图中不再需要时，这个作用域将会清理和销毁自己。
尽管永远不会需要清理作用域（因为Angular会为你处理），但是知道是谁创建了这个作用域
还是有用的，因为你可以使用这个$scope上叫做$destory()的方法来清理这个作用域。
#### 控制器
AngularJS中的控制器是一个函数，用来向视图的作用域中添加额外的功能。我们用它来给
作用域对象设置初始状态，并添加自定义行为。
注：将控制器命名为`[Name]Controller`而不是`[Name]Ctrl`是一个最佳实践。
```
app.controller('FirstController', function($scope) {
$scope.counter = 0;
$scope.add = function(amount) { $scope.counter += amount; };
$scope.subtract = function(amount) { $scope.counter -= amount; };
});
```
用这种设置方式我们可以在视图中调用add()或subtract()方法，这两个方法可以定义在FirstController的作用域中，或其父级的$scope中。
注意：AngularJS同其他JavaScript框架最主要的一个区别就是，控制器并不适合用来执行DOM操作、格式化或数据操作，以及除存储数据模型之外的状态维护操作。它只是视图和$scope之间的桥梁。设计良好的应用会将复杂的逻辑放到指令和服务中。通过使用指令和服务，我们可以将控制器重构成一个轻量且更易维护的形式。
#### 控制器嵌套（作用域包含作用域）
AngularJS应用的任何一个部分，无论它渲染在哪个上下文中，都有父级作用域存在。对于ng-app所处的层级来讲，它的父级作用域就是$rootScope。
有一个例外：在指令内部创建的作用域被称作孤立作用域。除了孤立作用域外，所有的作用域都通过原型继承而来，也就是说它们都可以访问父级作用
域。
默认情况下， AngularJS在当前作用域中无法找到某个属性时，便会在父级作用域中进行查找。如果AngularJS找不到对应的属性，会顺着父级作用域一直向上寻找，直到抵达$rootScope为止。如果在$rootScope中也找不到，程序会继续运行，但视图无法更新。
