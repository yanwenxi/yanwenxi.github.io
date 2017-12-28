---
title: angular-decorator
date: 2016-07-20 15:58:33
tags: angular decorator
---
#### angular提供的一种装饰-decorator
decorator 通过配置函数中的$provide 进行装饰，可扩展已有的服务
```
app.config(function ($provide) {
    //参数1 要装饰的内容
    $provide.decorator('$log',function ($delegate) { //代理。我们可以将这个代理函数包装好送回去
        var log = $delegate.log;
        $delegate.log = function () {
            //获取当前的形参
       var args = Array.of(...arguments);//es6的写法,作用同下
              //var args = Array.prototype.slice.call(arguments);
            args.unshift(new Date());
            log.apply(null,args);
        };
        return $delegate;//更改后 将原内容，送回去
    });
});
app.controller('computed1',function ($scope,my,$log) {
    //$log是angular给我们提供的一个服务，
这里的my相当于实例化了provider的函数，并且调用了$get方法
    $log.log('hello','ywx');
    //装饰的好处在原基础上,增加功能，修改angular自带的服务，装饰我们写好的
});
```