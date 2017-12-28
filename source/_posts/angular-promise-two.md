---
title: 通过$q,angular可以实现两种形式的promise
date: 2016-12-1 11:50:04
tags: angular $q promise
---
#### $q
在angular中，$q是一个非常重要的service。通过$q，angular有两种比较常用的方式来实现promise，分别是ES6风格与jquery风格。

**ES6风格实现代码如下：**
```
<div ng-app="app" ng-controller="demoController"></div>
angular.module('app', [])
.controller('demoController', function($q, $timeout) {
    var a = 1;
    $q(function(resolve, reject) {
        $timeout(function() {
            if(a == 1) {
                console.log('resolve');
                resolve('hello, JAKE');    
            } else {
                console.log('reject');
                reject('JAKE is not here!')
            }
        }, 2000)
    })
    .then(function(greeting) {
        console.log('success, ' + greeting);
    }, function(reason) {
        console.log('failed, ' + reason);
    })
})
```
当a==1，输出结果为
```
"resolve"
"success, hello, JAKE"
```

当a!=1，输出结果为
```
"reject"
"failed, JAKE is not here!"
```
上例中，我直接将第一步逻辑在$q()中处理。回调函数有两个参数，resolve, reject，分别代表执行成功与失败。
然后在对应的逻辑里面，使用resolve/reject标记逻辑的执行状态。then方法中的代码会在第一步的逻辑执行完了才执行。
**jquery风格代码如下**
  
  与jquery实现类似，我们需要定义个defer对象，并在第一步逻辑中手动返回promise
```
angular.module('app', [])
.controller('demoController', function($q, $timeout) {
    var a = 1;
    $q.when(function() {//$q.when()方法能把函数的返回值包装成一个promise,这样可以用then方法了。
        var defer = $q.defer();
        $timeout(function() {
            defer.notify('notify jake.');//notify用在resolve和reject之前，在两者之前被调用。
            
            if(a == 1) {
                console.log('rsolve');
                defer.resolve('hello, jake');
            }
            else {
                console.log('reject');
                defer.reject('jake is not here.')
            }
        }, 2000);
        return defer.promise;
    }())
    .then(function(greeting) {
        console.log('success, ' + greeting);
    }, function(reason) {
        console.log('fail, ' + reason);
    }, function(update) {
        console.log('notify, ' + update);
    })
})
```
当a==1，输出结果为
```
//先打印状态，再defer.resolve或者defer.reject之前执行defer.notify，之后才执行defer.resolve或者defer.reject。
"rsolve"
"notify, notify jake."
"success, hello, jake"
```
当a!=1 输出结果为
```
"reject"
"notify, notify jake."
"fail, jake is not here."
```
**Angular 中 Promise 的一些方法，其实从上面的实例就已经能够知道promise的大概用法，不算复杂。**

1. then(successCallback, errorCallback, notifyCallback)
从字面上我们就能够看出then三个回调的含义，第二个参数与第三个参数为可选。

2. catch(errorCallback) 
相当于 then(null, errorCallback)

3. finally(callback)
无论promise的返回状态是resolve还是reject，该方法总是会执行。
#### 链式操作
如果then中逻辑的执行需要时间等待，那么，then中回调函数的返回值为promise对象时，才能够按顺序执行。因此需要我们手动返回一个promise对象，例子如下
```
var app = angular.module('app', []);

app.controller('demoController', function($q, $timeout) {
    $q.when(function() {
        var defer = $q.defer();
        $timeout(function() {
            console.log('first');
            defer.resolve();
        }, 2000);
        return defer.promise;
    }())
    .then(function() {
        var defer = $q.defer();
        $timeout(function() {
            defer.resolve();
            console.log('second');
        }, 1000);
        return defer.promise;
    })
    .then(function() {
        console.log('third');    
    })
});
```
```
//结果如下：
"first"
"second"
"third"
```
官方文档中，认为then方法中只需要有返回值即可，但是如果then的执行不需要时间等待，then中的回调函数的第一个参数，会获取到上一个then的返回值，然后按顺序执行，如下：
```
var app = angular.module('app', []);

app.controller('demoController', function($q, $timeout) {
    
    function first() {
        var a = 1;
        var defer = $q.defer();
        $timeout(function() {
            if(a == 1) {
                console.log('first resolve');
                defer.resolve('resolve result');    
            } else {
                console.log('first reject');
                defer.reject();
            }
        }, 2000)
        return defer.promise;
    }
    
    var promiseA = first();
    
    promiseA.then(function(result) {
        // $timeout(function() {
        console.log('second, ' + result);
        return 'document';
        // }, 1000)
        
    })
    .then(function(result) {
        console.log('third, ' + result);
    })
    
});
```
```
//结果如下：
"first resolve"
"second, resolve result"
"third, document"
```
#### $http
$http是对promise的最佳实践。由于项目中有多个controller要用到请求回来的数据，在每一个控制器内都写一遍会使代码冗余，也太麻烦，因此我们常常会将获取数据的操作放在自定义的服务中。例子如下：
```
angular.module('app', [])
.controller('demoController', function(dataFactory) {
    dataFactory.getData().then(function(resp) {
        console.log(resp);
    })
})
.factory('dataFactory', function($http) {
    return {
        getData: function() {
            var url = 'https://www.webywx.com/list?id=1';
            return $http.get(url);    
        }
    }
})
```
我们将then中回调函数的参数打印出来，发现格式如下:
```
Object {
    config: Object {},
    data: Object {},
    headers: function(d) {},
    status: 200,
    statusText: 'OK'
}
```
这一点与jquery的返回值略有不同，他们分别表示的意思为
```
config： 请求的配置信息
data： 返回的数据
headers: 请求的头部信息
status: 返回的状态码
```
