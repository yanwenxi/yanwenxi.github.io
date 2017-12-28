---
title: ES6-promise学习
date: 2016-1-05 22:10:58
tags: ES6、promise
---
#### Promise的含义
所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise是一个对象，从它可以获取异步操作的消息。Promise提供统一的API，各种异步操作都可以用同样的方法进行处理。
#### Promise对象有以下两个特点
   1. 对象的状态不受外界影响。
   Promise对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称Fulfilled）和Rejected（已失败）。
   2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。
   Promise对象的状态改变，只有两种可能：从Pending变为Resolved和从Pending变为Rejected。
#### promise优缺点：
有了Promise对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise对象提供统一的接口，使得控制异步操作更加容易。

Promise也有一些缺点。首先，无法取消Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。第三，当处于Pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。
#### 基本用法：
ES6规定，Promise对象是一个构造函数，用来生成Promise实例。
```
var promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```
Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由JavaScript引擎提供，不用自己部署。
Promise实例生成以后，可以用then方法分别指定Resolved状态和Reject状态的回调函数。
```
promise.then(function(value) {
  // success
}, function(error) {
  // failure  失败的回调函数可以省略不写，是可选的。
});
```
#### Promise.prototype.then()
then方法的第一个参数是Resolved状态的回调函数，第二个参数（可选）是Rejected状态的回调函数。
then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因此可以采用链式写法，即then方法后面再调用另一个then方法。
#### Promise.prototype.catch()
Promise.prototype.catch方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数。
#### Promise.all()
 Promise.all方法用于将多个Promise实例，包装成一个新的Promise实例。
 ```
 var p = Promise.all([p1, p2, p3]);
 ```
####  Promise.race()
 Promise.race方法同样是将多个Promise实例，包装成一个新的Promise实例。
 ```
 var p = Promise.race([p1, p2, p3]);
 ```
 上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise实例的返回值，就传递给p的回调函数。
#### Promise.resolve()
 有时需要将现有对象转为Promise对象，Promise.resolve方法就起到这个作用。
 ```
 var jsPromise = Promise.resolve($.ajax('/whatever.json'));
 上面代码将jQuery生成的deferred对象，转为一个新的Promise对象。
 
 Promise.resolve等价于下面的写法。
 Promise.resolve('foo')
 // 等价于
 new Promise(resolve => resolve('foo'))
 ```
#### Promise.reject()
Promise.reject(reason)方法也会返回一个新的Promise实例，该实例的状态为rejected。它的参数用法与Promise.resolve方法完全一致。
```
var p = Promise.reject('出错了');
// 等同于
var p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s){
  console.log(s)
});
// 出错了
```
上面代码生成一个Promise对象的实例p，状态为rejected，回调函数会立即执行。
#### 两个有用的附加方法
   1. done()
     Promise对象的回调链，不管以then方法或catch方法结尾，要是最后一个方法抛出错误，都有可能无法捕捉到（因为Promise内部的错误不会冒泡到全局）。因此，我们可以提供一个done方法，总是处于回调链的尾端，保证抛出任何可能出现的错误。
   2. finally()
     finally方法用于指定不管Promise对象最后状态如何，都会执行的操作。
