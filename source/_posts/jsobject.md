---
title: js中的对象
date: 2016-02-09 13:21:25
tags: js object
---
#### 语法
对象可以通过两种形式定义： 声明（ 文字） 形式和构造形式。
对象的文字语法大概是这样：
```
var myObj = {
key: value
// ...
};
```
构造形式大概是这样：
```
var myObj = new Object();
myObj.key = value;
```
构造形式和文字形式生成的对象是一样的。 唯一的区别是， 在文字声明中你可以添加多个键 / 值对， 但是在构造形式中你必须逐个添加属性。“ 构造形式” 来创建对象是非常少见的,一般都用文字形式。

#### 类型
在 JavaScript 中一共有六种主要类型（ 术语是“ 语言类型”） ：string/number/boolean/null/undefined/object.
注意， 简单基本类型（ string、 boolean、 number、 null 和 undefined） 本身并不是对象。null 有时会被当作一种对象类型， 但是这其实只是语言本身的一个 bug， 即对 null 执行
typeof null 时会返回字符串 "object"。 实际上， null 本身是基本类型。
实际上， JavaScript 中有许多特殊的对象子类型， 可以称之为复杂基本类型。比如函数、数组...
可以简单地理解为：javascript 引用类型的数据存放在堆内存，基本类型的数据存放在栈内存。

<font color="red">内置对象</font>
JavaScript 中还有一些对象子类型， 通常被称为内置对象。 有些内置对象的名字看起来和简单基础类型一样， 不过实际上它们的关系更复杂.
内置对象：String/Number/Boolean/Object/Function/Array/Date/RegExp/Error,这些内置函数可以当作构造函数（ 由 new 产生的函数调用） 来使用， 从而可以构造一个对应子类型的新对
象。例子如下：
```
var strPrimitive = "I am a string";
typeof strPrimitive; // "string"
strPrimitive instanceof String; // false
var strObject = new String( "I am a string" );
typeof strObject; // "object"
strObject instanceof String; // true
// 检查 sub-type 对象
Object.prototype.toString.call( strObject ); // [object String]
```
从代码中可以看到， strObject 是由 String 构造函数创建的一个对象;
```
var strPrimitive = "I am a string";
console.log( strPrimitive.length ); // 13
console.log( strPrimitive.charAt( 3 ) ); // "m"
```
原始值 "I am a string" 并不是一个对象， 它只是一个字面量， 并且是一个不可变的值。如果要在这个字面量上执行一些操作， 比如获取长度、 访问其中某个字符等， 那需要将其
转换为 String 对象。js在必要时语言会自动把字符串字面量转换成一个 String 对象，所以字符串也能直接使用length属性。数值字面量也是同样道理：42.359.toFixed(2)。

null 和 undefined 没有对应的构造形式， 它们只有文字形式。 相反， Date 只有构造， 没有文字形式。对于 Object、 Array、 Function 和 RegExp（ 正则表达式）来说，无论使用文字形式还是构造形式， 它们都是对象， 不是字面量。Error 对象很少在代码中显式创建， 一般是在抛出异常时被自动创建。 也可以使用 new Error(..) 这种构造形式来创建，不过一般来说用不着。

##### 内容
对象的内容是由一些存储在特定命名位置的（ 任意类型的） 值组成的，我们称之为属性。
```
var myObject = {
a: 2
};
myObject.a; // 2
myObject["a"]; // 2
```

如果要访问 myObject 中 a 位置上的值， 我们需要使用 . 操作符或者 [] 操作符。 .a 语法通常被称为“ 属性访问”， `["a"] `语法通常被称为“ 键访问”。 实际上它们访问的是同一个位
置， 并且会返回相同的值 2， 所以这两个术语是可以互换的。当属性为number时，必须使用[]操作符。
注：这两种语法的主要区别在于 . 操作符要求属性名满足标识符的命名规范， 而 `[".."]`语法可以接受任意 UTF-8/Unicode 字符串作为属性名。 举例来说， 如果要引用名称为 "SuperFun!" 的属性， 那就必须使用 `["Super-Fun!"]`语法访问， 因为 Super-Fun! 并不是一个有效的标识符属性名。
小提示：对象的属性永远为字符串类型的。如果不是它也会隐式转换成字符串。

<font color="red">可计算属性名</font>
ES6 增加了可计算属性名， 可以在文字形式中使用 [] 包裹一个表达式来当作属性名：
```
var prefix = "foo";
var myObject = {
[prefix + "bar"]:"hello",
[prefix + "baz"]: "world"
};
myObject["foobar"]; // hello
myObject["foobaz"]; // world
```
可计算属性名最常用的场景可能是 ES6 的符号（ Symbol），简单来说， 它们是一种新的基础数据类型， 包含一个不透明且无法预测的值（从技术角度来说就是一个字符串）。一般来说你不会用到符号的实际值（ 因为理论上来说在不同的 JavaScript 引擎中值是不同的）， 所以通常接触到的是符号的名称， 比如 Symbol.Something（ something是随便起的） ：
```
var myObject = {
[Symbol.Something]: "hello world"
}
```
<font color="red">数组</font>
数组也是对象， 所以虽然每个下标都是整数， 你仍然可以给数组添加属性：
```
var myArray = [ "foo", 42, "bar" ];
myArray.baz = "baz";
myArray.length; // 3
myArray.baz; // "baz"
```
可以看到虽然添加了命名属性（ 无论是通过 . 语法还是 [] 语法）， 数组的 length 值并未发生变化。
注意： 如果试图向数组添加一个属性， 但是属性名“ 看起来” 像一个数字， 那它会变成一个数值下标（ 因此会修改数组的内容而不是添加一个属性） ：
```
var myArray = [ "foo", 42, "bar" ];
myArray["3"] = "baz";
myArray.length; // 4
myArray[3]; // "baz"
```
<font color="red">判断对象属性是否存在：</font>
```
var myObject = {
a:2
};
("a" in myObject); // true
("b" in myObject); // false
myObject.hasOwnProperty( "a" ); // true
myObject.hasOwnProperty( "b" ); // false
```
in 操作符会检查属性是否在对象及其 `[[Prototype]]`原型链中）。 相比之下，hasOwnProperty(..) 只会检查属性是否在 myObject 对象中， 不会检查 `[[Prototype]]`链。
<font color="red">枚举：</font>
```
var myObject = { };
Object.defineProperty(
myObject,
"a",
// 让 a 像普通属性一样可以枚举
{ enumerable: true, value: 2 }
);
Object.defineProperty(
myObject,
"b",
// 让 b 不可枚举
{ enumerable: false, value: 3 }
);
myObject.b; // 3
("b" in myObject); // true
myObject.hasOwnProperty( "b" ); // true
// .......
for (var k in myObject) {
console.log( k, myObject[k] );
} /
/ "a" 2
```
可以看到， myObject.b 确实存在并且有访问值， 但是却不会出现在 for..in 循环中（ 尽管可以通过 in 操作符来判断是否存在）。 原因是“ 可枚举” 就相当于“ 可以出现在对象属性
的遍历中”。
注：在数组上应用 for..in 循环有时会产生出人意料的结果， 因为这种枚举不仅会包含所有数值索引， 还会包含所有可枚举属性。 最好只在对象上应用 for..in 循环，如果要遍历数组就使用传统的 for 循环来遍历数值索引。

也可以通过另一种方式来区分属性是否可枚举：
```
var myObject = { };
Object.defineProperty(
myObject,
"a",
// 让 a 像普通属性一样可以枚举
{ enumerable: true, value: 2 }
);
Object.defineProperty(
myObject,
"b",
// 让 b 不可枚举
{ enumerable: false, value: 3 }
);
myObject.propertyIsEnumerable( "a" ); // true
myObject.propertyIsEnumerable( "b" ); // false
Object.keys( myObject ); // ["a"]
Object.getOwnPropertyNames( myObject ); // ["a", "b"]
```
propertyIsEnumerable(..) 会检查给定的属性名是否直接存在于对象中（ 而不是在原型链上） 并且满足 enumerable:true。
Object.keys(..) 会返回一个数组， 包含所有可枚举属性， Object.getOwnPropertyNames(..)会返回一个数组， 包含所有属性， 无论它们是否可枚举。
in 和 hasOwnProperty(..) 的区别在于是否查找 `[[Prototype]]`链， 然而， Object.keys(..)和 Object.getOwnPropertyNames(..) 都只会查找对象直接包含的属性。

#### 遍历
for..in 循环可以用来遍历对象的可枚举属性列表（ 包括 `[[Prototype]]` 链）。
注：遍历数组下标时采用的是数字顺序（ for 循环或者其他迭代器）， 但是遍历对象属性时的顺序是不确定的， 在不同的 JavaScript 引擎中可能不一样。 因此，在不同的环境中需要保证一致性时， 一定不要相信任何观察到的顺序， 它们是不可靠的。

ES6 增加了一种用来遍历数组的 for..of 循环语法——遍历数组中的值
```
var myArray = [ 1, 2, 3 ];
for (var v of myArray) {
console.log( v );
} /
/ 1
// 2
// 3
```
数组有内置的 @@iterator， 因此 for..of 可以直接应用在数组上。 和数组不同， 普通的对象没有内置的 @@iterator， 所以无法自动完成 for..of 遍历。 

#### 补充与扩展
属性的特性可以通过属性描述符来控制， 比如 writable 和 configurable。 此外， 可以使用Object.preventExtensions(..)、 Object.seal(..) 和 Object.freeze(..)来设置对象（ 及其属性） 的不可变性级别。属性不一定包含值——它们可能是具备 getter/setter 的“ 访问描述符”。 此外， 属性可以是可枚举或者不可枚举的， 这决定了它们是否会出现在 for..in 循环中。可以使用 ES6 的 for..of 语法来遍历数据结构（ 数组、 对象， 等等） 中的值， for..of会寻找内置或者自定义的 @@iterator 对象并调用它的 next() 方法来遍历数据值。






