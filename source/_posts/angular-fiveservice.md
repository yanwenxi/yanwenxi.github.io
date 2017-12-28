---
title: angular中的5种服务
date: 2016-06-01 14:53:19
tags: angular 服务
---
#### 5种创建自定义服务的方式
1.Provider 2.Factory 3.Service 4.value 5.constant,其中只有provider可以配置，factory是项目中最常用的。
服务的作用：提取公共内容，封装公有方法，并且服务是单例的。服务对应的是构造函数。服务名字的后面函数其实是一个服务的构造函数，单例相当于只能new一次创建一个实例，不能创建多个实例来使用。
service层主查写逻辑代码的，但在angularJS里也可以持久化数据（充当数据容器），以供不同的controller调用。
controller层：即控制层，在angularJS里就是写控制器的。控制器里尽量不要写那些不必要的逻辑，尽量写在service层里。
```
//定义构造函数
var myServiceProvider = function(){
this.$get = function(){
return ....
};
};
//在模块中登记
angular.module("myModule",[])
.provider("myService",myServiceProvider);
```
#### 1.provider
provider 可配置  当注入后会默认调用$get方法   仅仅只有provider可配置.
只有provder是能传.config() 函数的服务。如果想在 service 对象启用之前，先进行模块范围的配置，那就应该选择 provider。需要注意的是：在config函数里注入provider时，名字应该是：providerName+Provider. 
使用Provider的优点就是，你可以在Provider对象传递到应用程序的其他部分之前在app.config函数中对其进行修改。 当你使用Provider创建一个service时，唯一的可以在你的控制器中访问的属性和方法是通过$get()函数返回内容。
```
app.provider('my',function () {
    //当我们使用这个服务的时候，会直接调用$get方法
    this.$get = function () {
             return {  }
    }
});
```
##### 可配置的服务
 有时我们希望服务在不同的场景下可以有不同的行为，这意味着服务可以进行配置。比如，我们希望小计算器可以根据不同的本地化区域，给计算结果追加货币符号前缀， 那么需要在这个服务创建之前，首先配置本地化区域的值，然后在具体的计算中， 根据这个值选择合适的货币符号。
      AngularJS使用模块的config()方法对服务进行配置，需要将实例化的服务提供者（而不是服务实例）注入到配置函数中：
 ```
      angular.module("myModule",[])
      .config(["myServiceProvider",function(myServiceProvider){
      //do some configuration.
      }]);
```
#### 2.factory
factory方式创建的服务，作用就是返回一个有属性有方法的对象。相当于：var f = myFactory();
```
app.factory('my',function () {
return  {}  //返回$get执行后的函数
});
```
```
注：使用provider服务时的执行顺序 先config > run > controller
//需要注意的是：在配置provider时，名字应该是：providerName+Provider   
    app.config(function(myProviderProvider){
        myProviderProvider.setName("ywx");       
    });
    app.provider('myProvider', function() {
        var name="";
        var test={"a":1,"b":2};
        //注意的是，setter方法必须是(set+变量首字母大写)格式
        this.setName = function(newName){
            name = newName  
        }

        this.$get =function($http,$q){
            return {
                getData : function(){
                    var d = $q.defer();
                    $http.get("url")//读取数据的函数。
                        .success(function(response) {
                            d.resolve(response);
                        })
                        .error(function(){
                            d.reject("error");
                        });
                    return d.promise;
                },
                "lastName":name,
                "test":test
            }   
        }

    });
    app.controller('myCtrl', function($scope,myProvider) {
        alert(myProvider.lastName);
        alert(myProvider.test.a)
        myProvider.getData().then(function(data){
            //alert(data)
        },function(data){
            //alert(data)
        });
    });
```
```
//通过工厂模式创建自定义服务
    app.factory('myFactory', function() {
        var service = {};//定义一个Object对象'
        service.name = "张三";
        var age;//定义一个私有化的变量
        //对私有属性写getter和setter方法
        service.setAge = function(newAge){
            age = newAge;
        }
        service.getAge = function(){
            return age; 
        }
        return service;//返回这个Object对象
    });
//创建控制器
    app.controller('myCtrl', function($scope, myFactory) {
        myFactory.setAge(20);
        $scope.r =myFactory.getAge();
        alert(myFactory.name);
    });
```
注：在自定义服务里可以注入内置服务（例$http等），但不能注入$scope作用域对象。
#### 3.service
通过service方式创建自定义服务，相当于new的一个对象：var s = new myService();，只要把属性和方法添加到this上才可以在controller里调用。
```
app.service('myService',function () {
     此处为return出来的那个对象，对象的属性需要加上this，this[key]=val这种形式。
     })
```
```
app.service('myService', function($http,$q) {
        this.name = "service";
        this.myFunc = function (x) {
            return x.toString(16);//转16进制
        }
        this.getData = function(){
            var d = $q.defer();
            $http.get("ursl")//读取数据的函数。
                .success(function(response) {
                d.resolve(response);
            })
                .error(function(){
                alert(0)
                d.reject("error");
            });
            return d.promise;
        }
    });
    app.controller('myCtrl', function($scope, myService) {
        $scope.r = myService.myFunc(255);
        myService.getData().then(function(data){
            console.log(data);//正确时走这儿
        },function(data){
            alert(data)//错误时走这儿
        });
    });
```
#### 4.value 
以 key与value的形式来写value服务。这个值就是服务实例。
```
app.value('my',{
          此处为return出来的对象
     });
```
```
有时我们需要在不同的组件之间共享一个变量，可以将这种情况视为一种服务： provider返回的总是变量的值。
value方法提供了对这种情况的简化封装：
angular.module("myModule",[])
.value("myValueService","cx129800123");

INSIDE：AngularJS会将value方法封装为provider，上面的示例 等同于：
angular.module("myModule",[])
.provider("myService",function(){
this.$get = function(){
return "cx129800123";
};
});
```
#### 5.constant  
用法同value，也是以 key ,value的形式，但是可以配置（不是真正意义上的配置），即可以增加属性。配置时不需要再名字后面加Provider。这个常量就是服务实例。
```
 //配置constant
    app.config(function (my) {
        my.currency = '￡';//在当前属性上增加一个属性
    });
    app.controller('computed',function ($scope,my) {
        //my就是return这个对象,这样就可以使用return出来对象的属性了
    })
```
```
有时我们需要在不同的组件之间共享一个常量，可以将这种情况视为一种服务： provider返回的总是常量的值。constant方法提供了对这种情况的简化封装：
angular.module("myModule",[])
.constant("myConstantService","Great Wall");
和value方法不同，AngularJS并没有将constant方法封装成一个provider，而仅仅是在内部登记这个值。这使得常量在AngularJS的启动配置阶段就可以使用（创建任何 服务之前）：你可以将常量注入到模块的config()方法中。
```
#### 在过滤器中注入自定义服务
```
<body>
<div ng-app="myApp">
    在过滤器中使用服务:
    <h1>{{255 | myFormat}}</h1>
    //若使用过滤器时需要传入参数，用：隔开，多个参数用多个：隔开。
</div>

<script>
    var app = angular.module('myApp', []);
    app.service('hexafy', function() {
        this.myFunc = function (x) {
            return x.toString(16);
        }
    });
    app.filter('myFormat',['hexafy', function(hexafy) {
        return function(x) {
            return hexafy.myFunc(x);
        };
    }]);
</script>
</body>
```