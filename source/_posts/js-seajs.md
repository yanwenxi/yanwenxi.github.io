---
title: js模块化开发-sea.js(基础)
date: 2015-12-12 10:28:12
tags: js sea.js 模块化
---
js模块化开发能解决的问题：1.冲突2.性能 3.依赖
使用sea.js解决的步骤：
- 1.引入sea.js的库
- 2.如何变成模块-define
- 3.如何调用模块-exports  -seajs.use
- 4.如何依赖模块？-require
一、如何变成模块-define 例：test.js
```
define(function (require, exports, module) {
    //參數固定，写法不变
    //exports:对外的接口
    function abc() {
        alert(123);
    }
    exports.abc=abc;
});
```
二、如何调用模块-exports  -seajs.use
    seajs.use:例index页面去调用模块，第一个参数：模块的地址，第二个参数：模块加载成功后的回调函数。
 注：seajs模块的默认根目录是sea.js的目录。也就是说sea.js库在哪个文件夹下，seajs.use找模块的时候就在那个文件下开始找。
 ```
seajs.use('sea/test.js',function (ex) {
    //回调的参数，就是模块中的exports.
    ex.abc();//当加载成功后，会弹出123
    abc();  //这样做就很好的避免了命名冲突。
    function abc() {
        alert(456);
    }
});
```
三.如何依赖模块？-require
例：test1.js  var a=10; test2.js依賴于test1.js,在此即说明test2.js要用到test1.js里定义的变量。test2.js如下：
```
define(function (require,exports,module) {
    require('./test1.js');//根据当前文件相对的路径来写，这句相当于 let a=10;
    function abcd() {
        alert(a);
    }
});
```
如果test1.js是一个模块的化：
```
define(function (require,exports,module) {
    var a=10;
    exports.a=a;
});
//test2.js依賴于test1.js
define(function (require,exports,module) {
    //require('./test1.js');//如果地址是一个模块的话，那么返回值就是模块中的exports.
    function abcd() {
        alert(require('./test1.js').a);
    }
});
```
