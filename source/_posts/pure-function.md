---
title: JavaScript 中的“纯函数”
date: 2017-01-03 11:19:27
tags: js 纯函数
---
#### 什么是纯函数（pure function）
纯函数是指 不依赖于且不改变它作用域之外的变量状态 的函数。
<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/pure%20function.png-public)
</div>
也就是说， 纯函数的返回值只由它调用时的参数决定 ，它的执行不依赖于系统的状态（比如：何时、何处调用它）。
纯函数是 函数式编程 的一个基础。
#### 下面的例子让其理解更深刻
```
var values = { a: 1 };
function impureFunction ( items ) {
  var b = 1;
  items.a = items.a * b + 2;
  return items.a;
}
var c = impureFunction( values );
// 现在 `values.a` 变成 3,  impureFunction 改变了它。
```
在上面的代码中，我们改变了参数对象中的一个属性。由于我们定义的函数改变的对象在我们的函数作用域之外，导致这个函数成为“不纯”的函数。
```
var values = { a: 1 };
function pureFunction ( a ) {
  var b = 1;
  a = a * b + 2;
  return a;
}
var c = pureFunction( values.a );
// `values.a` 没有被改变, 它的值仍然是 1
```
上面的代码，我们只计算了作用域内的局部变量，没有任何作用域外部的变量被改变，因此这个函数是“纯函数”。
```
var values = { a: 1 };
var b = 1;
function impureFunction ( a ) {
  a = a * b + 2;
  return a;
}
var c = impureFunction( values.a );
// 实际上,  `c` 的值依赖于外部变量 `b`.
// 你可能容易忽略这种情况，外部变量的变化也可能会导致函数出现不确定结果。
```
上面的代码里， b 不在作用域中，函数执行结果依赖于上下文环境，因此函数也是“不纯”的。
```
var values = { a: 1 };
var b = 1;
function pureFunction ( a, d ) {
  a = a * d + 2;
  return a;
}
var c = pureFunction( values.a, b );
// 这样从定义上明确 `c` 依赖于参数 `b`，避免函数不确定结果。
```
上面这样改就成了“纯函数”。
#### 使用纯函数的好处
最主要的好处是没有副作用。纯函数不会修改作用域之外的状态，做到这一点，代码就变得足够简单和清晰：当你调用一个纯函数，你只要关注它的返回值，而不用担心因为别处的问题导致错误。

纯函数是健壮的，改变执行次序不会对系统造成影响，因此纯函数的操作可以并行执行。

纯函数非常容易进行单元测试，因为不需要考虑上下文环境，只需要考虑输入和输出。

最后，尽可能使用纯函数 让你的代码保持简单和灵活 。
