---
title: Angular中如何使用promise
date: 2016-06-29 00:50:58
tags: angular、promise、$q、deferred
---
#### Angular 中的 promise
Angular的事件循环给予了Angular特有的能力，能在$rootScope.$evalAsync阶段中执行promise, promise会坐等$digest运行循环结束。
我们来建一个例子，从GitHub上返回一组针对AngularJS的开放pull请求。
```
<h1>Open Pull Requests for Angular JS</h1>
<ul ng-controller="DashboardController">
<li ng-repeat="pr in pullRequests">
{{ pr.title }}
</li>
</ul>
```
如果有个服务返回了一个promise，可以在.then()方法中与这个promise交互，它允许我们修改作用域上的任意变量，放置到视图上，并且期望Angular会为我们执行它：
```
angular.module('myApp', [])
.controller('DashboardController', [
'$scope', 'GithubService',
function($scope, UserService) {
// GithubService的getPullRequests()方法
// 返回了一个promise
User.getPullRequests(123)
.then(function(data) {
$scope.pullRequests = data.data;
});
}]);
```
当 对 getPullRequests 的 异 步 调 用 返回 时 ， 在 .then()方 法 中 就 可 以 用 $scope.pull Requests这个值了，然后它会更新$scope.pullRequests数组。
Angular 中的 promise
Angular的事件循环给予了Angular特有的能力，能在$rootScope.$evalAsync阶段中执行
promise（关于运行循环的更多细节，参见第24章）。 promise会坐等$digest运行循环结束。
这件事让我们能毫无压力地把promise的结果转换到视图上。它也能让我们不加思考地把
XHR调用的结果直接赋值到$scope对象的属性上。
```
<h1>Open Pull Requests for Angular JS</h1>
<ul ng-controller="DashboardController">
<li ng-repeat="pr in pullRequests">
{{ pr.title }}
</li>
</ul>
```
如果有个服务返回了一个promise，可以在.then()方法中与这个promise交互，它允许我们修改作用域上的任意变量，放置到视图上，并且期望Angular会为我们执行它：
```
angular.module('myApp', [])
.controller('DashboardController', [
'$scope', 'GithubService',
function($scope, UserService) {
// GithubService的getPullRequests()方法
// 返回了一个promise
User.getPullRequests(123)
.then(function(data) {
$scope.pullRequests = data.data;
});
}]);
```
当 对 getPullRequests 的 异 步 调 用 返回 时 ， 在 .then()方 法 中 就 可 以 用 $scope.pull
Requests这个值了，然后它会更新$scope.pullRequests数组。

#### 如何创建一个 promise
想要在Angular中创建promise，可以使用内置的$q服务。 $q服务在它的deferred API中提供了一些方法。
首先，需要把$q服务注入到想要使用它的对象中。
```
angular.module('myApp', [])
.factory('GithubService', ['$q', function($q) {
// 现在就可以访问到$q库了
}]);
```
要创建一个deferred对象，可以调用defer()方法：
```
var deferred = $q.defer();
```
deferred对象暴露了三个方法，以及一个可以用于处理promise的promise属性。
可以用于处理promise的promise属性：这句话其实就是说deferred对象有一个promise属性。即deferred.promise。
deffered 对象属性promise ：最后返回的是一个新的deferred对象 promise 属性，而不是原来的deferred对象。这个新的Promise对象只能观察原来Promise对象的状态，而无法修改deferred对象的内在状态可以防止任务状态被外部修改。
1. resolve（value）
resolve函数用这个值来执行deferred promise。
```
deferred.resolve({name: "Ari", username: "@auser"});
```
2. reject（reason）
这个方法用一个原因来拒绝deferred promise。它等同于使用一个“拒绝”来执行一个promise。
```
deferred.reject("Can't update user");
// 等同于
deferred.resolve($q.reject("Can't update user"));
```
3.  notify（value）
这个方法用promise的执行状态来进行响应。notify 回调可能被调用 0到多次,提供一个进度指示在解决或拒绝（resolve和rejected）之前。
#### 方法介绍：
$http(options)返回的是一个被封装的promise对象，可以直接使用.then等方法。
1. then(successFn, errFn, notifyFn)
无论promise成功还是失败了，当结果可用之后， then都会立刻异步调用successFn或者errFn。这个方法始终用一个参数来调用回调函数：结果，或者是拒绝的理由。在promise被执行或者拒绝之前， notifyFn回调可能会被调用0到多次，以提供过程状态的提示。
then()方法总是返回一个新的promise，可以通过successFn或者errFn这样的返回值执行或者被拒绝。它也能通过notifyFn提供通知。
2. catch(errFn)
这个方法就只是个帮助函数，能让我们用.catch(function(reason){})取代err回调：
```
$http.get('/repos/angular/angular.js/pulls')
.catch(function(reason) {
deferred.reject(reason);
});
```
3. finally(callback)
finally方法允许我们观察promise的履行或者拒绝，而无需修改结果的值。当我们需要释放一个资源，或者是运行一些清理工作，不管promise是成功还是失败时，这个方法会很有用。
我们不能直接调用这个方法，因为finally是IE中JavaScript的一个保留字。纠结到最后，只好这样调用它了：
```
promise['finally'](function() {});
```
Angular的$q deferred对象是可以串成链的，这样即使是then，返回的也是一个promise。这个promise一被执行， then返回的promise就已经是resolved或者rejected的了。
#### $q服务类似于原始的Kris Kowal的Q库：
   1. $q是跟Angular的$rootScope模型集成的，所以在Angular中，执行和拒绝都很快。
   2. $q promise是跟Angular模板引擎集成的，这意味着在视图中找到的任何promise都会在视
图中被执行或者拒绝。
   3. $q很小，所以没有包含Q库的完整功能。
### 链式请求
then方法在初始promise被执行之后，返回一个新的派生promise。这种返回形式给了我们一种特有的能力，把另一个then接在初始的then方法结果之后。
```
// 一个响应promise的服务
GithubService.then(function(data) {
var events = [];
for (var i = 0; i < data.length; i++) {
events.push(data[i].events);
}
return events;
}).then(function(events) {
$scope.events = events;
});
```
在本例中，我们可以创建一个执行链，它允许我们中断基于更多功能的应用流程，可以籍此导向不同的结果。这个中断能让我们在执行链的任意时刻暂停或者推迟promise的执行。
#### $q库自带了几个不同的有用方法。
1. all(promises)
如果我们有多个promise，想要把它们合并成一个，可以使用$q.all(promises)方法来把它们合并成一个promise。这个方法带有一个参数。即promises（数组或者promise对象），一个promise数组或者promise的hash。
all()方法返回单个promise，会执行一个数组或者一个散列的值。每个值会响应promise散列中的相同序号或者键。如果任意一个promise被拒绝了，结果的promise也会被拒绝。
2. defer()
  defer()方法创建了一个deferred对象，它没有参数，返回deferred对象的一个实例。
3. reject(reason)
这个方法创建了一个promise，被以某一原因拒绝执行了。它专门用于让我们能在一个promise链中转发拒绝的promise，类似JavaScript中的throw。在同样意义上，我们能在JavaScript中捕获一个异常，也能够转发这个拒绝，我们需要把这个错误重新抛出。可以通过$q.reject(reason)来做到这点。
这个方法带有单个参数：reason（常量、字符串、异常、对象），拒绝的原因。
reject()方法返回一个已经用某个原因拒绝的promise。
4. when(value)
when()函数把一个可能是值或者能接着then的promise包装成一个$q promise。这样我们就能处理一个可能是也可能不是promise的对象。when()函数有一个参数： value，该参数是个值，或者是promise
when()函数返回了一个promise，我们可以像使用其他promise一样使用它。
