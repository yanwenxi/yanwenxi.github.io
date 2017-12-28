---
title: 使用Benchmark.js和jsPerf分析代码性能
date: 2017-02-05 10:43:12
tags: Benchmark jsPerf
---
#### 前言
前端开发中，掌握好浏览器的特性进行有针对性的性能调优是一项基本工作，同时，比较不同代码的执行速度也是一项关键的工作。

比如，当我们想比较RegExp的test方法和String对象的indexOf方法查找字符串谁的速度更快的话，js代码在不同的浏览器，不同的操作系统环境运行的效率可能是不一样的，这就是为什么我们需要对其进行基准测试，在做基准测试方面，我们可以使用Benchmark.js和使用jsPerf（一个基于JSLitmus的基准测试库）。我们可以使用jsPerf来分享你的基准测试。

#### Benchmark.js 的使用
其实在很多github 开源项目中，往往都能看到benchmark文件夹。使用方法如下：
首先在系统根目录下，通过npm intsall benchmark 来安装benchmark。该模块会被写入node_modules文件夹中，我们在test.js文件中通过require方法引入该模块。
将如下代码写入test.js文件，该文件置于系统根目录下：
```
var Benchmark = require('benchmark');
var suite = new Benchmark.Suite;

// 添加测试
suite.add('RegExp#test', function() {
    /o/.test('Hello World!');
})
    .add('String#indexOf', function() {
        'Hello World!'.indexOf('o') > -1;
    })
// add listeners
    .on('cycle', function(event) {
        console.log(String(event.target));
    })
    .on('complete', function() {
        console.log('Fastest is ' + this.filter('fastest').pluck('name'));
    })
// run async
    .run({ 'async': true });
```
然后在控制台执行node test.js 可见输出结果如下：
```
➜  ~ git:(master) ✗ node test.js
RegExp#test x 9,847,928 ops/sec ±1.47% (83 runs sampled)
String#indexOf x 23,366,017 ops/sec ±0.91% (96 runs sampled)
Fastest is String#indexOf
```
结果最快的就是String对象的indexOf方法，其中，Ops/sec 测试结果以每秒钟执行测试代码的次数（Ops/sec）显示，这个数值越大越好。除了这个结果外，同时会显示测试过程中的统计误差，以及相对最好的慢了多少（%）;

<font color="red">call和apply的比较</font>
```
var Benchmark = require('benchmark');
var suite = new Benchmark.Suite;
var arr1 = function (str) {
    return [].slice.apply(str);
};
var str2 = function (str) {
    return [].slice.call(str);
};
// 添加测试
suite.add('arr1', function() {
    arr1('test');
})
    .add('str2', function() {
        str2('test');
    })
// add listeners
    .on('cycle', function(event) {
        console.log(String(event.target));
    })
    .on('complete', function() {
        console.log('Fastest is ' + this.filter('fastest').pluck('name'));
    })
// run async
    .run({ 'async': true });
```
输出如下内容：
```
arr1 x 596,505 ops/sec ±1.14% (95 runs sampled)
str2 x 627,822 ops/sec ±1.27% (92 runs sampled)
Fastest is str2
```
<font color="red">本地的使用</font>
本地使用Benchmark需要引入如下三个文件：
```
<script src="lodash.js"></script>
<script src="platform.js"></script>
<script src="benchmark.js"></script>
```
#### jsPerf 的使用
jsPerf 提供了一个简便的方式来创建和共享测试用例，并可以比较不同JavaScript代码段的性能。jsPerf也是基于Benchmark来运行的。
打开jsPerf站点：http://jsperf.com/，先将必填的项目填了。其中，slug是短名称，会生成一个网址，因此不可与别人的重复。然后在Code snippets to compare 区域填入title和用于测试的code。最后点击save test case 完成验证即可。浏览器会自动跳转到测试页面.
Async选项框是用来测试一些异步调用的性能的，我们的代码没有使用异步方法，所以不必勾选。

<font color="red">运行测试</font>
点击“Run tests”按钮开始测试两种算法的性能。建议在运行性能测试之前，关闭无关的浏览器页面，关闭其他程序，退出不必要的后台进程，以保证结果不受其他环境的影响。你也可以通过点击个别测试用例的名字单独运行这个例子.
jsPerf还会统计所有运行过这个测试用例的浏览器的比较结果，显示在下方的Browserscope区域，可以通过它直观地看出各个版本浏览器的性能横向和纵向比较情况。

<font color="red">查看别人的测试用例</font>
我们可以通过 http://jsperf.com/browse 浏览最新提交的250项最新测试用例。我们也可以使用底部的Revisions来查看不同的版本，也就是不同浏览器的测试用例情况。

#### 总结
John Resig 在其博文 JavaScript基准测试的质量中提到，应该尽量考虑到每个测试结果的误差并去减小它。扩大测试的样本值，健全的测试执行，都能够起到减少误差的作用。
