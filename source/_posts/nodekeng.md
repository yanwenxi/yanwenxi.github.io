---
title: node 中的一些坑
date: 2016-10-23 23:06:26
tags: node
---
### 核心模块

require

node中通过require引入模块的机制为将需要的模块对应的js文件引入后执行一遍，因此在自己写的依赖模块中尽量不要无故输出信息，否则会使控制台十分混乱。

### fs

fs模块中的所有方法在指定路径时都必须为绝对路径或是完整的相对路径（即包含../、./、/的相对路径）。

fs.mkdir/mkdirSync

API
```
fs.mkdir(path, callback)
```
创建文件夹操作，支持文件夹嵌套，例如fs.mkdir('./a/b/c'),回调函数中的参数为Error信息，只有发生错误的时候才会存在，创建成功则为null。

mkdir操作的路径如果有文件夹嵌套，则当上级路径不存在时，不能直接创建。例如当前文件夹下没有a文件夹，则fs.mkdir('./a/b')不能直接创建a和a/b文件夹。
为了减少代码的重复量，可使用下面的函数来对mkdir操作进行封装。
```
let path = require('path'),
    fs = require('fs');
function mkdir(p,fn){
    fs.exists(p, function (flag) { // 判断当前路径下的文件夹是否已经存在
        if(flag){ // 如果当前文件夹已经存在则执行回调函数
            return fn();
        }
        // 如果当前文件夹不存在，则将当前文件夹的上级目录作为新的参数p，创建当前文件夹的操作放入回调函数，递归调用mkdir函数
        mkdir(path.dirname(p), function () {
            fs.mkdir(p,fn);
        });
    });
}
```
这里用到了path模块的dirname方法，这个方法的作用是获取一个路径的上级路径。

fs.readFile/readFileSync

读取文件操作，在使用同步方法readFileSync时函数直接返回读到的数据，如果发生错误则返回错误的信息。

但是异步方法readFile在callback中的参数却是error和data，即不管是否成功第一个参数都是error，第二个参数才是读到的数据。

在node中应尽量用stream来代替文件的直接读写操作。

### 请求与响应

#### 跨域请求

- cors

如果需要采用cors来支持跨域请求，则需要对响应头进行如下设置（完整版）
```
res.writeHead(200, {
    "Access-Control-Allow-Origin": "*",
    "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
    "Access-Control-Allow-Headers": "Content-Type, accept"
});
```
另外，跨域时尽量对请求方法和请求路径都进行验证（RESTful），因为某些客户端框架（ionic）在发送跨域请求时，会先发送一个option方法的请求来尝试跨域请求是否成功，在成功之后才会发送原本的请求。

- cookie

一般在node中设置cookies都会使用基于express的第三方包cookie-parser。不使用第三方包的原生写法为
```
res.setHeader("Set-Cookie", [...cookies])
```
其中cookies的格式为key=value; option，注意;后的空格。

option的参数中max-age=time表示cookie的过期时间，其中time的为number，单位是秒。

获取客户端传递的cookies的方法为
```
req.headers.cookie
```
如果使用express和cookie-parser，则关于cookie的方法都会被集成在request和response上，设置cookie的方法为
```
res.cookie(key, value, option)
```
注意这里的option中的max-age单位变为了毫秒，其他则没有变化。

获取客户端传递的cookies的方法为
```
req.cookies
```
获取的cookies为对象形式。

IDE没有提示功能或者提示功能不全的朋友请注意上文中的cookie和cookies并熟练记住。
