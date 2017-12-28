---
title: Lodash的学习笔记-一些常用的API
date: 2017-01-11 13:37:06
tags: Lodash
---
#### Array
- _.head/_.last
```
_.head([1, 2, 3]);
// → 1

_.last([1, 2, 3]);
// → 3
```
分别可以抽取数组第一个元素的值和最后一个元素的值。咋看之下觉得很无聊。但是跟下面两个结合起来就厉害。

- _.tail/_.initial
```
_.tail([1, 2, 3]); //tail 尾部，尾
// → [2, 3]   获取数组 array第一个元素除外的所有元素.

_.initial([1, 2, 3]);  //initial  最初的，开始的
// → [1, 2]  去除数组最后一个元素array.
```
结合上面的两个函数就产生很大作用：首先看一个针对数组尾部特殊化处理的例子：
```
let names=['fred', 'barney', 'pebbles'];

_.initial(names).join(', ') +
(_.size(names) > 1 ? ', & ' : '') + _.last(names);

// → 'fred, barney, & pebbles'
```
怎么样比简单的写for+if else优雅很多吧。接着我们来看看递归求和：
```
function sum(arr){
 return _.head(arr) ? 
 ( _.head(arr) + sum( _.tail(arr) ) ) :
0;
}
```
注：
1）其实好像数组都可以表现为
```
[a, [b, [c .....]]]
```
然后再将其扁平化的结果。 是不是？所以结合这四个函数，很多数组遍历的操作都可以改写成递归的方式。虽然性能可能略差，但是可读性反而更强了(并非所有的递归都一定损耗性能，详细可以去了解下JIT优化)。

2）但是在ES6中，更推荐优先使用数组的解构。
- _.zip
```
_.zip(['fred', 'barney'], [30, 40]);
// → [['fred', 30], ['barney', 40]]
```
_.zip(*array) 将几个数组按照位置组成新的数组，返回数组列表。此方法对重组数据结构非常有帮助。

- _.unzip
既然lodash是个函数库，那有zip肯定也有他的逆运算unzip。unzip其实可以看作对ES6数组解构的补充，应用的场景也是十分相似的。
```
var zipped = _.zip(['fred', 'barney'], [30, 40], [true, false]);
// => [['fred', 30, true], ['barney', 40, false]]

_.unzip(zipped);
// => [['fred', 'barney'], [30, 40], [true, false]]
```
- _.without
```
_.without([1, 2, 1, 3], 1, 2);
// => [3]
```
#### Collection
- _.map/_.reduce/_.filter
这三个函数其实早就名声在外，遍历集合可不仅仅只有_.each。任何时候都优先考虑这些函数。
```
function timesThree(n) {
  return n * 3;
}

_.map([1, 2], timesThree);
// => [3, 6]

_.map({ 'a': 1, 'b': 2 }, timesThree);
// => [3, 6] (iteration order is not guaranteed)译：迭代顺序不保证

var users = [
  { 'user': 'barney' },
  { 'user': 'fred' }
];

// using the `_.property` callback shorthand
_.map(users, 'user');
// => ['barney', 'fred']
```
```
_.reduce([1, 2], function(total, n) {
  return total + n;
});
// => 3

_.reduce({ 'a': 1, 'b': 2 }, function(result, n, key) {
  result[key] = n * 3;
  return result;
}, {});
// => { 'a': 3, 'b': 6 } (iteration order is not guaranteed)
```
```
_.filter([4, 5, 6], function(n) {
  return n % 2 == 0;
});
// => [4, 6]

var users = [
  { 'user': 'barney', 'age': 36, 'active': true },
  { 'user': 'fred',   'age': 40, 'active': false }
];

// using the `_.matches` callback shorthand
_.pluck(_.filter(users, { 'age': 36, 'active': true }), 'user');
// => ['barney']

// using the `_.matchesProperty` callback shorthand
_.pluck(_.filter(users, 'active', false), 'user');
// => ['fred']

// using the `_.property` callback shorthand
_.pluck(_.filter(users, 'active'), 'user');
// => ['barney']
```
- _.every/_.some
```
let users = [
 { 'user': 'barney', 'active': true },
 { 'user': 'fred', 'active': false }
];

_.every(users, { 'user': 'barney', 'active': false });
// → false

_.some(users, ['active', false]);
// → true

其实作用相当于：
_.every => && 
_.some => ||
```
#### Function
- _.curry
是函数柯里化的函数。
```
let abc = function(a, b, c) {
 return [a, b, c];
};

let curried = _.curry(abc);

curried(1)(2)(3);
// → [1, 2, 3]

curried(1, 2)(3);
// → [1, 2, 3]

curried(1, 2, 3);
// → [1, 2, 3]

// Curried with placeholders.
curried(1)(_, 3)(2);
// → [1, 2, 3]
```
柯里化有3个常见作用：1. 参数复用；2. 构建高阶函数；3. 延迟计算。 参数复用的例子：
```
let parse = function(data, config){
    xxxx
 return xxxx;
};

let config = {};
let parseByConfig = _.curryRight(parse)(config);
```
如代码所示，这是一个数据解析函数，通过某些配置按照一定的规则去解析数据。如果在某个场景下参数配置都一样。那可以用柯里化先引入一个参数。然后再接收不同的数据。这样就不用在每次调用时都引入同样的config参数。

- _.curryRight
```
var abc = function(a, b, c) {
  return [a, b, c];
};

var curried = _.curryRight(abc);

curried(3)(2)(1);
// => [1, 2, 3]

curried(2, 3)(1);
// => [1, 2, 3]

curried(1, 2, 3);
// => [1, 2, 3]

// using placeholders
curried(3)(1, _)(2);
// => [1, 2, 3]
```
- _.partial
便于理解可以叫它部分函数，还是先看例子。
```
let greet = function(greeting, name) {
 return greeting + ' ' + name;
};

let sayHelloTo = _.partial(greet, 'hello');
sayHelloTo('fred');
// → 'hello fred'

// Partially applied with placeholders.
let greetFred = _.partial(greet, _, 'fred');
greetFred('hi');
// → 'hi fred'
let greet = function(greeting, name) {
 return greeting + ' ' + name;
};

let sayHelloTo = _.partial(greet, 'hello');
sayHelloTo('fred');
// → 'hello fred'

// Partially applied with placeholders.
let greetFred = _.partial(greet, _, 'fred');
greetFred('hi');
// → 'hi fred'
```
好像跟柯里化很像。是的，它其实就是柯里化的具体应用——构建高阶函数。_.curry会把一个函数转化成可以柯里化函数，而当这个柯里化函数接受了一定的参数后，它就变成了一个部分应用函数了。

- _.flow
```
function square(n) {
  return n * n;
}

var addSquare = _.flow(_.add, square);
addSquare(1, 2);
// => 9
```
这是用来组合高阶函数的一个方法。如果用数学的角度去思考的话，就有点像, 把函数 f(), g(), 和h() 组合起来可以得到复合函数 f( g( h() ) )。

- _.flowRight
```
function square(n) {
  return n * n;
}

var addSquare = _.flowRight(square, _.add);
addSquare(1, 2);
// => 9
```

#### Chain
- _.chain
```
$('div').css('color','red')
 .on('click', function(){})
.fadeIn();
```
作为一个合格的前端攻城狮，你对这个一定不陌生。它一定给你带来过很多美好的记忆。那我们如何让这样一段优雅的代码应用到非DOM对象上呢。用_.chain就行了。
```
_.head(
_.map(
 _.sortBy('users', 'age'),
 function(o) {
 return `${o.user} is ${o.age}`;
}
)
)

_.chain(users)
.sortBy('age')
 .map(function(o) {
 return o.user + ' is ' + o.age;
})
.head()
.value();
```
两段代码在做一样的事情。你更喜欢哪个呢。不过要注意_.chain最后的value。因为_.chain用一个容器包装了你的对象，最后需要通过value返回出真正的值。如果不能理解，联想一下下面这段代码：
```
$('div')[0].classList
```
用jQuery选择器筛选到的是jQuery对象，而如果要调用真正的DOM对象的属性的话，还要用索引去调用它。

