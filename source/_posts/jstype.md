---
title: js中的类型
date: 2015-02-09 16:54:59
tags: js
---
#### 内置类型
JavaScript 有七种内置类型：
空值（ null ）
未定义（ undefined ）
布尔值（ boolean ）
数字（ number ）
字符串（ string ）
对象（ object ）
符号（ symbol ， ES6 中新增）
```
typeof undefined === "undefined"; // true
typeof true === "boolean"; // true
typeof 42 === "number"; // true
typeof "42" === "string"; // true
typeof { life: 42 } === "object"; // true
typeof null === "object"; // true  注意：null比较特殊
// ES6中新加入的类型
typeof Symbol() === "symbol"; // true
```
注意：typeof 运算符总是会返回一个字符串。
<font color="red">typeof null</font>
typeof null === "object"; 返回true;它比较特殊，正确的返回结果应该是 "null" ， 但这个 bug由来已久， 在 JavaScript 中已经存在了将近二十年， 也许永远也不会修复，因为这牵涉到太多的 Web 系统， “修复”它会产生 更多的 bug， 令许多系统无法正常工作。
因此我们需要使用复合条件来检测 null 值的类型：
```
var a = null;
(!a && typeof a === "object"); // true
```
null 是基本类型中唯一的一个“假值”类型， typeof 对它的返回值为 "object" 。

还有一种情况：
```
typeof function a(){ /* .. */ } === "function"; // true
```
这样看来， function （ 函数） 也是 JavaScript 的一个内置类型。其实并不是这样的， 通过查阅规范就会知道，它实际上是 object 的一个“子类型”。 具体来说， 函数是“可调用对象”， 它有一个内部属性 `[[Call]]` ， 该属性使其可以被调用。
函数不仅是对象， 还可以拥有属性。 例如：
```
function a(b,c) {
/* .. */
} 
```
函数对象的 length 属性是其声明的参数的个数：a.length; 值为2.
因为该函数声明了两个命名参数， b 和 c ， 所以其 length 值为 2

```
typeof [1,2,3] === "object"; // true
```
 数组也是对象。 确切地说， 它也是 object 的一个“子类型” ， 数组的元素按数字顺序来进行索引（ 而非普通像对象那样通过字符串键值） ， 其 length 属性是元素的个数。
 
#### 值和类型
JavaScript 中的变量是没有类型的， 只有值才有 。 变量可以随时持有任何类型的值。换个角度来理解就是， JavaScript 不做“类型强制”； 也就是说， 语言引擎不要求变量 总是持有与其初始值同类型 的值。 一个变量可以现在被赋值为字符串类型值， 随后又被赋值为数字类型值。

已在作用域中声明但还没有赋值的变量， 是 undefined 的。 相反， 还没有在作用域中声明过的变量， 是 undeclared（未声明） 的。如下：
```
var a;
a; // undefined
b; // ReferenceError: b is not defined(也可理解为未声明的意思)
```
```
var a;
typeof a; // "undefined"
typeof b; // "undefined"
```
请注意虽然 b 是一个 undeclared 变量， 但typeof b 并没有报错。 这是因为 typeof 有一个特殊的安全防范机制。






