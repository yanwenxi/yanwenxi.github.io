---
title: 简述angular自定义过滤器在页面和控制器中的使用
date: 2016-05-26 10:06:02
tags: angular filter
---
AngularJS另一个特点就是提供了过滤器，可以通过操作UNIX下管道的方式，操作数据结果。
通过使用管道，可以便于双向的数据绑定中视图的展现。
过滤器在处理过程中，将数据变成新的格式，而且可以使用管道这种链式风格，还能接受附加的参数。
##### 首先设置自定义过滤器
```
app..module('myApp')
    　　　　　　　.filter('filterName',function(){
    　　　　　　　　　return function(要过滤的对象,参数1,参数2,...){
                            // ...做一些事情
    　　　　　　　　　　　return Obj; //这里返回的是过滤后的对象
    　　　　　　　　　}
    　　　　　　　});
```
##### 在页面中使用：
```
{{obj | filterName}} ====> obj是要过滤的对象
```
\｜这里的竖线是一种管道功能，如果对linux比较熟悉的话，这块的｜根linux的管道功能，基本是一样的

##### 在控制器中使用：
```
//首先要注入$filter,使用的时候
$filter("filterName")(要过滤的对象,参数1,参数2,...);
```
filter过滤器主要用来过滤一个数组数据并返回一个包含子数组数据的新数组。
比如，在客户端搜索时，我们可以快速的从数组中过滤出我们想要的结果。
这个filter方法接收一个string，object，或者function参数用来选择/移除数组元素。
我们也可以在JavaScript中使用$filter服务来调用过滤器，还拿字符串大写来举例:
```
app.controller('DemoController', ['$scope', '$filter', 
 function($scope, $filter) {
  
  $scope.name = $filter('lowercase')('Ari');
}]);

```
如何传递参数到filter呢？只需要把参数放在filter之后，中间加个冒号（如果有多个参数要传递，在每个参数后加上冒号）比如，数字过滤器可以帮助我们限制数字的位数，如果想显示两位小数，加上number:2就可以了
```
{{ 123.456789 | number:2 }}

```
注： 意：| lowercase |uppercase管道命令可以有多个
```
{{ name |uppercase | lowercase  }}

```

 



