---
title: Lodash 学习笔记 - Chain 方法
date: 2017-01-11 11:23:52
tags: lodash chain
---
#### Chain
可以说是 lodash 中最为重要的部件，想要用lodash进行复杂的多步操作都离不开chain的帮助。
首先说说Chain的调用方式有两种：一种是显式调用(Explicit Chaining)，一种是隐式调用(Implicit Chaining)。
```
//显式调用例子如下:
//下面的例子采用了ECMAScript2015的语法：
let numbers = [1, 2, 3, 4, 5];
let sumOfEvenSquares = _.chain(numbers) //注意numbers放置的位置
    .filter(n => n % 2 === 0)
    .map(n => n * n)
    .sum()
    .value();    
//sumOfEvenSquares: 20
//特别要注意结尾的那个.value()
```
```
//隐式调用例子如下：
let sumOfEvenSquares = _(numbers) //注意numbers的位置，不需要显式的使用chain关键字
    .filter(n => n % 2 === 0)
    .map(n => n * n)
    .sum()
```
```
//隐式调用另一个写法：
let isEven = n => n % 2 === 0;
let square = n => n * n;
let sumOfEvenSquares = _(numbers).filter(isEven).map(square).sum();
```
为什么要多出来一个 .value()，而不是直接出结果呢？那是因为可能要等待延时(Deferred execution)数据的到来，再执行取值。这就是我们常说的Lazy evaluation （延迟计算/惰性求值）
```
//如下面的例子：首先生成链式表达式
var wallet = _(assets).filter(ownedBy('me'))
                      .pluck('value')
                      .reduce(sum);

$json.get("/new/assets").success(function(data) {
    assets.push.apply(assets, data);     // 然后更新数据
    wallet.value();                      // 最后求值
});
```
#### _(value)
参数:value (*) : 待包装至 lodash 实例的值
返回:(Object) : 返回一个新的 lodash 包装集实例

创建一个包含 value 的 lodash 对象以开启内置的方法链。方法链对返回数组、集合或函数的方法产生作用，并且方法可以被链式调用。那些获取单值或可能返回一个原始值的方法将自动结束方法链并且返回一个未包裹成 lodash 对象的值。如果明确需要链式调用可以使用 _.chain。而显式链则用_.chain的方式实现延迟计算，即：求值操作等到 .value()被调用时再执行。

延迟计算支持一些方法快速合并。快速合并是一个最优合并迭代器调用的策略。这样做可以帮助避免一些计算中间生成的数据结构，并且能够大大降低迭代器的执行次数。
```
var wrapped = _([1, 2, 3]);

// 返回一个未包装的值
wrapped.reduce(function(total, n) {
  return total + n;// JavaScript 数组reduce()方法同时应用一个函数针对数组的两个值(从左到右)，以减至一个值。
});
// → 6

// 返回一个包装值
var squares = wrapped.map(function(n) {
  return n * n;
});

_.isArray(squares);
// → false

_.isArray(squares.value());
// → true
```

#### _.chain(value)
参数:value (*) : 待包装的值
返回:(Object) : 返回一个新的 lodash 包装实例
创建一个明确能够被链式调用的 lodash 对象.
```
var users = [
  { 'user': 'barney',  'age': 36 },
  { 'user': 'fred',    'age': 40 },
  { 'user': 'pebbles', 'age': 1 }
];

var youngest = _.chain(users)
  .sortBy('age')
  .map(function(chr) {
    return chr.user + ' is ' + chr.age;
  })
  .first()
  .value();
// → 'pebbles is 1'
```

