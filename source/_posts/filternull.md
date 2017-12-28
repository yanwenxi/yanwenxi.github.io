---
title: AngularJS使用ng-repeat和ng-if实现数据的筛选显示，多适用于表单数据的显示
date: 2016-06-05 10:22:15
tags: angularjs 
---
这篇文章主要介绍了AngularJS使用ng-repeat和ng-if实现数据的删选显示效果,非常适用于表单数据的显示使用,涉及ng-repeat和ng-if命令的相关使用技巧,在项目中用到过，特此记录下，遇到问题要多尝试！总会找到最好的方法。
#### ng-repeat可以实现内容的重复显示
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
  <script src="https://cdn.static.runoob.com/libs/angular.js/1.4.6/angular.min.js"></script>
</head>
<body>
<div ng-app="myApp" ng-controller="myCtrl">
  <ul>
    <li ng-repeat="x in names">
      {{ x.name }}我的年龄：{{x.age}}
    </li>
  </ul>
</div>

<script>
  angular.module('myApp', []).controller('myCtrl', function($scope) {
    $scope.names = [
      {name:'',age:'20'},
      {name:'Hege',age:'21'},
      {name:'Kai',age:'22'}
    ];
  });
</script>
</body>
</html>
```
上述例子名字显示为空，怎么让名字为空的不显示呢？
#### 可以使用ng-repeat+ng-if的方法，这样可以实现简单的前端筛选
```
 <li ng-repeat="x in names" ng-if="x.name'">
      {{ x.name }}{{x.age}}
    </li>
```
这样就可以只显示名字不为空的了。如果只想显示名字为Kai的，代码如下：
```
 <li ng-repeat="x in names" ng-if="x.name=='Kai'">
      {{ x.name }}{{x.age}}
    </li>
```
#### 实现过滤的另一种思路：在controller里直接过滤空值数据，页面直接ng-repeat过滤后的数据即可。
代码如下：
```
<li ng-repeat="x in names">
      {{ x.name }}我的年龄：{{x.age}}
    </li>
angular.module('myApp', []).controller('myCtrl', function($scope) {
    $scope.names = [
      {name:'',age:'20'},
      {name:'Hege',age:'21'},
      {name:'Kai',age:'22'}
    ];
    $scope.names=$scope.names.filter(function(item){
    	return item.name != '';
    })
  });
```
这种方式也能只显示有值的名字。

