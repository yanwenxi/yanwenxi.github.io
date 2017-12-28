---
title: AngularJS中的$http、$q服务和promise对象的联系
date: 2016-10-23 09:52:38
tags: angular $http $q promise
---
#### angular中的$http
$http服务是AngularJS系统自带的，可以用来进行网络通信、获取远程服务器的数据。要记住的是，$http是对浏览器XMLHttpRequest的封装，也就是说，它其实是Ajax。

首先为了用$http发起请求，我们得先找到一个可以测试的API，这里我用：https://randomuser.me/api/来测试：用get请求，它能够返回json格式的一个随机用户的资料。比如在浏览器中输入，可以看到返回的数据：
 ![](http://oi28tswuq.bkt.clouddn.com/images/data.png)
 注：这是一个公开的测试接口，以后测试也可以用。
 
 #### $http的基本用法：
 ```
 $http(options).success(successFn).error(errorFn)
 ```
 options：包括url、method（get或post等）; successFn：成功的回调; errorFn：失败的回调
 ```
 angular.module('myDemo', [])
 
     .controller('myController', ['$scope', '$http', function ($scope, $http) {
         $scope.testHttp = function () {
             $http({
                 method: 'GET',
                 url: 'https://randomuser.me/api/'
             }).success(function (data) { // // 这里的data的类型是object，并且，它已经是返回数据的“数据”部分
                 console.log(angular.toJson(data, true));
                 $scope.result = data;
             }).error(function (err) {
                 console.log(err);
             })
         }
     }]);
 ```
 这里，$http(options)返回的是一个被封装的promise对象，我们可以console.log出来看看：
 ![](http://oi28tswuq.bkt.clouddn.com/images/promise.png)
 
 从上图可以看到，黑色部分圈起来的是基本的promise部分（拥有then，catch，finally函数），除了这些函数以外，这个promise对象还有success和error函数（见红线处），这是$http返回的时候封装的，供我们使用的时候更方便。比如上述例子中，注释里说，success的回调函数返回的data直接就是我们想要的“数据部分”，那么如果我用的是then，那么返回的除了数据部分，还有额外的部分（比如状态码），这时候我就得自己拿出这个“数据部分”，代码如下：
 ```
 var promise = ...;  // 省略$http过程
 promise.then(function (data) {
         // realData被包含在data的“data”字段中，一般它才是我们想要的
         var realData = data.data;
         console.log(realData);
     }, function (err) {
 
     });
 ```
#### $q的介绍与用处:
在上述例子中，我们是在Controller层写了网络请求。但是我们知道，在AngularJS的MVVM框架之中，业务逻辑（包括网络请求）的部分应该尽可能写到Service层中，而让Controller的逻辑尽可能少。但是，怎么把网络请求最终得到的有用的数据，返回给controller呢？这里有两种思路：

方法一：把$http(options)返回的promise对象直接在Service中返回。在controller中拿到之后，再写success和error的回调。
方法二：在Service中返回一个自定义的promise对象，用来回调给controller。这里的回调者，由$q创建的“defered”对象来担任。

第一种方法比较简单，现在直接用第二种方法来写，这种方法也是在项目中常用的，在此之前，先简单介绍一下AngularJS的q服务。q服务是在AngularJS中用来创建promise对象的，但它首先创建一个defered对象：
```
var deferred=$q.defer()
```
打印该defered对象：
![](http://oi28tswuq.bkt.clouddn.com/images/defered.png)
可以看到它有3个方法：resolve，reject，notify，分别对应于接受、拒绝、通知。还有一个promise属性，它的值就是一个基本的promise对象，主要用来返回。比如上面的例子，我可以这样写：
server.js如下：
```
.factory('userService', ['$http', '$q', function ($http, $q) {

        var userService = {};   // 返回的服务

        this.urlForRandomUser = "https://randomuser.me/api/";

        var that = this; // 获取当前作用域，下面要用到
        userService.getRandomUser = function () {

            var defered = $q.defer();

            $http.get(that.urlForRandomUser)
                .success(function (data) {
                    defered.resolve(data.results[0]);
                })
                .error(function (err) {
                    defered.reject(err);
                });
            return defered.promise; // 把defered对象中的promise对象返回出来
        };
```
controller.js如下：
```
.controller('firstController', ['$scope', 'userService', function ($scope, user) {
 //采用数组的方式可以防止压缩变量名称改变导致的问题，此处的变量user就代表userService.当然user也可以换成其他变量比如a,b,c等等，无论换成哪个变量，最终他都代表userService。当然要是不压缩代码的话可以直接注入userService服务，使用的时候是这个样子的：userService.getRandomUser();
        $scope.getUser = function () {

            function success(data) {
                var str = angular.toJson(data, true);   // 用angular的格式化json的方法，更为清晰
                $scope.result = str;
            }

            function error(err) {
                $scope.result = err;
                alert('error occured!\n' + err);
            }

            // 注意这里不能用success或error函数，用then就好,defered的promise是基本的promise对象，并没有success和error函数，所以要用then来执行回调。
            user.getRandomUser().then(success, error);
        }

    }])
```


 
 