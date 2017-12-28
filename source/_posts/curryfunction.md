---
title: 浅析JavaScript中的柯里化函数
date: 2017-01-03 13:04:42
tags: js 柯里化
---
#### 什么是curry function
curry 的概念很简单：只传递给函数一部分参数来调用它， 让它返回一个函数去处理剩下的参数.
柯里化通常也称部分求值，其含义是给函数分步传递参数，每次传递参数后部分应用参数，并返回一个更具体的函数接受剩下的参数，这中间可嵌套多层这样的接受部分参数函数，直至返回最后结果。因此柯里化的过程是逐步传参，逐步缩小函数的适用范围，逐步求解的过程。
#### 简单的例子
```
var add = function(x) {
return function(y) {
return x + y;
};
};
var increment = add(1);
var addTen = add(10);
increment(2);
// 3
addTen(2);
// 12
add(1)(2);//3
```
这里我们定义了一个 add 函数， 它接受一个参数并返回一个新的函数。 调用 add 之后， 返回的函数就通
过**闭包**的方式记住了 add 的第一个参数。(ps:这里利用了闭包的特点)
#### Function.prototype.bind方法是柯里化应用
与 call/apply 方法直接执行不同，bind 方法 将第一个参数设置为函数执行的上下文，其他参数依次传递给调用方法（函数的主体本身不执行，可以看成是延迟执行），并动态创建返回一个新的函数， 这符合柯里化特点。
```
bind方法用法如下：用于改变函数中this关键字的
var foo = {x: 888};
var bar = function () {
    console.log(this.x);
}.bind(foo);               // 绑定
bar();                     // 888
```
下面是一个 bind 函数的模拟，testBind 创建并返回新的函数，在新的函数中将真正要执行业务的函数绑定到实参传入的上下文，延迟执行了。
```
Function.prototype.testBind = function (scope) {
    var fn = this;                    //// this 指向的是调用 testBind 方法的一个函数， 
    return function () {
        return fn.apply(scope);
    }
};
var testBindBar = bar.testBind(foo);  // 绑定 foo，延迟执行
console.log(testBindBar);             // Function (可见，bind之后返回的是一个延迟执行的新函数)
testBindBar();                        // 888
```


