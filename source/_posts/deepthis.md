---
title: 关于this那些事
date: 2017-02-07 10:43:40
tags: js this
---
#### this的简单理解
this 是在运行时进行绑定的， 并不是在编写时绑定， 它的上下文取决于函数调用时的各种条件。 this 的绑定和函数声明的位置没有任何关系， 只取决于函数的调用方式。
```
call()、apply()和bind()用来改变this关键字的
function.call(thisObj,参数1，参数2....)
function.apply(thisObj,[参数1，参数2....])
function.bind(thisObj，参数1,参数2...)
```
call和apply改变this关键字时函数会被立即执行，而bind不会使函数立即执行，而是会创建一个新函数。
```
function foo() {
 this.count++;
} 
foo.count = 0; 函数是一个对象类型的，所以可以添加自定义属性。
foo.call(foo);//这样foo.count=2,因为call把foo函数里的this指向foo本身。
foo();//函数直接执行，函数里面的this指向window,所以foo.count还是为0;
```
总结：this 实际上是在函数被调用时发生的绑定， 它指向什么完全取决于函数在哪里被调用。
#### 严格与非严格模式下的this
```
function foo() {
console.log( this.a );
}
var a = 2;
foo(); // 2，此时函数里的this默认指向全局window。
```
如果使用严格模式（ strict mode）， 那么全局对象将无法使用默认绑定， 因此 this 会绑定到 undefined：
```
function foo() {
"use strict";
console.log( this.a );
}
var a = 2;
foo(); // TypeError: this is undefined
```
虽然 this 的绑定规则完全取决于调用位置， 但是只有 foo() 运行在非 strict mode 下时， 默认绑定才能绑定到全局对象； 严格模式下与 foo()的调用位置无关：
```
function foo() {
console.log( this.a );
}
var a = 2;
(function(){
"use strict";
foo(); // 2
})();
```
通常来说你不应该在代码中混合使用 strict mode 和 non-strict mode。 整个程序要么严格要么非严格。 然而， 有时候你可能会用到第三方库， 其严格程度和你的代码有所不同， 因此一定要注意这类兼容性细节。
```
function foo() {
console.log( this.a );
}
var obj2 = {
a: 42,
foo: foo
};
var obj1 = {
a: 2,
obj2: obj2
};
obj1.obj2.foo(); // 42
```
函数调用时，函数中的this取决于该函数点前面是谁，点前面是谁this就代表谁，此处点前面是obj2，所以运行结果为42.
当函数执行时前面没有点时，在非严格模式下，this指向(默认绑定)全局window;如果在严格模式下，this会指向undefined,当然会报TypeError: this is undefined。 当函数运行时前面没有点时，要注意有没有显示绑定方法如apply,call和bind，因为它们会显示改变函数中的this。

注意： 对于默认绑定来说， 决定 this 绑定对象的并不是调用位置是否处于严格模式， 而是函数体是否处于严格模式。 如果函数体处于严格模式， this 会被绑定到 undefined， 否则
this 会被绑定到全局对象。

#### 判断this的规则
现在我们可以根据优先级来判断函数在某个调用位置应用的是哪条规则。 可以按照下面的顺序来进行判断：
- 1.函数是否在 new 中调用（ new 绑定） ？ 如果是的话 this 绑定的是新创建的对象。var bar = new foo()，构造函数中的this指向当前创建的实例。
- 2.函数是否通过 call、 apply（ 显式绑定） 或者硬绑定调用？ 如果是的话， this 绑定的是指定的对象。var bar = foo.call(obj2)
- 3.函数是否在某个上下文对象中调用（ 隐式绑定） ？ 如果是的话， this 绑定的是那个上下文对象。var bar = obj1.foo()
- 4.如果都不是的话， 使用默认绑定。 如果在严格模式下， 就绑定到 undefined， 否则绑定到全局对象。var bar = foo()
- 5.定时器中的this一直指向window.

#### 被忽略的this
如果你把 null 或者 undefined 作为 this 的绑定对象传入 call、 apply 或者 bind， 这些值在调用时会被忽略， 实际应用的是默认绑定规则：
```
function foo() {
console.log( this.a );
} v
ar a = 2;
foo.call( null ); // 2
```
那么什么情况下会传入 null 呢？
一种非常常见的做法是使用 apply(..) 来“ 展开” 一个数组， 并当作参数传入一个函数。类似地， bind(..) 可以对参数进行柯里化（ 预先设置一些参数）， 这种方法有时非常有用：
```
function foo(a,b) {
console.log( "a:" + a + ", b:" + b );
} 
// 把数组“ 展开” 成参数
foo.apply( null, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind( null, 2 );
bar( 3 ); // a:2, b:3
```
这两种方法都需要传入一个参数当作 this 的绑定对象。 如果函数并不关心 this 的话， 你仍然需要传入一个占位值， 这时 null 可能是一个不错的选择， 就像代码所示的那样。
小提示：在 ES6 中， 可以用 ... 操作符代替 apply(..) 来“ 展开” 数组， foo(...[1,2]) 和 foo(1,2) 是一样的， 这样可以避免不必要的this 绑定。 可惜， 在 ES6中没有柯里化的相关语法， 因此还是需要使用bind(..)。

然而， 总是使用 null 来忽略 this 绑定可能产生一些副作用。 如果某个函数确实使用了this（ 比如第三方库中的一个函数）， 那默认绑定规则会把 this 绑定到全局对象（ 在浏览
器中这个对象是 window）， 这将导致不可预计的后果（ 比如修改全局对象）。（假设要使用bind方法的函数（称为fun1）中使用了第三方库的函数，恰好这个第三库的函数中也有this，此时代码fun1.bind(null，参数1)很有可以会影响到第三库函数中的this）显而易见， 这种方式可能会导致许多难以分析和追踪的 bug。

#### 更安全的this
一种“ 更安全” 的做法是传入一个特殊的对象， 把 this 绑定到这个对象不会对程序产生任何副作用。可以创建一个“ DMZ”（ demilitarizedzone， 非军事区） 对象——它就是一个空的非委托的对象.如果我们在忽略 this 绑定时总是传入一个 DMZ 对象， 那就什么都不用担心了， 因为任何对于 this 的使用都会被限制在这个空对象中， 不会对全局对象产生任何影响。
在 JavaScript 中创建一个空对象最简单的方法都是 Object.create(null)，Object.create(null) 和 {} 很 像， 但 是 并 不 会 创 建 Object.prototype 这个委托， 所以它比 {}“ 更空” ：
```
function foo(a,b) {
console.log( "a:" + a + ", b:" + b );
} 
// 定义的 DMZ 空对象
var ø = Object.create( null );//ø为变量名，可以随便起。
// 把数组展开成参数
foo.apply( ø, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind( ø, 2 );
bar( 3 ); // a:2, b:3
```
使用变量名 ø 不仅让函数变得更加“ 安全”， 而且可以提高代码的可读性， 因为 ø 表示“ 我希望 this 是空”， 这比 null 的含义更清楚。 当然你可以用任何喜欢的名字来命名 DMZ 对象。

#### 箭头函数中的this
箭头函数并不是使用 function 关键字定义的， 而是使用被称为“ 胖箭头” 的操作符 => 定义的。 箭头函数不使用 this 的标准规则， 而是根据外层（ 函数或者全局） 作用域来决定 this。
```
function foo() {
// 返回一个箭头函数
return (a) => {
//this 继承自 foo()
console.log( this.a );
};
}
var obj1 = {
a:2
};
var obj2 = {
a:3
};
var bar = foo.call( obj1 );
bar.call( obj2 ); // 2, 不是 3 ！
```
foo() 内部创建的箭头函数会捕获调用时 foo() 的 this。 由于 foo() 的 this 绑定到 obj1，bar（ 引用箭头函数） 的 this 也会绑定到 obj1， 箭头函数的绑定无法被修改。（ new 也不
行！ ）
箭头函数最常用于回调函数中， 例如事件处理器或者定时器：
```
function foo() {
setTimeout(() => {
// 这里的 this 在此法上继承自 foo()
console.log( this.a );
},100);
}
var obj = {
a:2
};
foo.call( obj ); // 2
```
