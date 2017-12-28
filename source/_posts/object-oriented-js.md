---
title: 面向对象的javascript及js中6种继承方式
date: 2015-06-23 13:06:26
tags: javascript、object-oriented、继承
---
#### 面向对象的javascript及js中6种继承方式
   javascript 没有提供传统的面向对象语言中的类式继承，而是通过原型委托的方式来实现对象与对象之间的继承。
   编程语言按照数据类型大致可以为两类：一类是静态类型语言，另一类是动态类型语言。
   静态类型语言在编译时便已经确定变量的类型，而**动态类型**语言的变量类型要到程序运行的时候，待变量被赋予某个值之后，才会具有某种类型。
   静态类型语言有java，.net，c等等。动态语言类型有javascript。js是一门典型的动态类型语言。
   ##### 静态语言类型的优缺点：
   优点：一是在编译时就能发现类型不匹配的错误。二是程序中规定了数据类型，编译器可以针对这些信息对程序进行一些优化工作，提高程序的执行速度。
   缺点：一是迫使程序员依照强契约来编写程序，为每个变量规定数据类型。二是类型的声明也会增加更多的代码。
   ##### 动态语言类型的优缺点：
   优点：一是编写的代码数量会减少，使代码看起来更加简洁。
   缺点：无法保证变量的类型，从而在程序的运行期可能发生跟类型相关的错误。
#### 鸭子类型（duck typing）
   在动态类型语言的面向对象设计中，鸭子类型的概念至关重要。鸭子类型的通俗说法是：如果它走起路来像鸭子，叫起来也是鸭子，那么它就是鸭子。利用鸭子类型的思想，不必借助超类型的帮助，就能轻松地在动态类型语言中实现一个原则：“面向接口编程”。面向接口编程是设计模式中最重要的思想。
   ```
   //模拟鸭子类型小故事
   var duck={
       duckSinging:function () {
           console.log("嘎嘎嘎");
       }
   };
   var chicken={
       duckSinging:function () {
           console.log("嘎嘎嘎");
       }
   };
   var choir=[];
   var joinChoir=function (animal) {
       if(animal && typeof animal.duckSinging==="function"){
           choir.push(animal);
           console.log('恭喜加入合唱团');
           console.log('合唱团已有成员数量：'+choir.length);
       }
   };
   joinChoir(duck);
   joinChoir(chicken);
   ```
#### 多态
多态的含义：同一操作作用于不同的对象上面，可以产生不同的解释和不同的执行结果。
```
//一段多"多态"的javascript代码
var makeSound=function (animal) {
    if(animal instanceof Duck){
        console.log('嘎嘎嘎');
    }else if(animal instanceof Chicken){
        console.log('咯咯咯');
    }
};
var Duck=function () {};
var Chicken=function () {};
makeSound(new Duck());//嘎嘎嘎
makeSound(new Chicken());//咯咯咯
```
多态背后的思想是将做什么和谁去做以及怎样去做分离开来，也就是将不变的事物和可以改变的事物分离开来。在这个小故事
中，动物都会叫，这是不变的，但是不同类的动物具体怎么叫是可变的。
```
//把不变的抽离出来
var makeSound=function (animal) {
    animal.sound();
};
var Duck=function () {};
Duck.prototype.sound=function () {
    console.log('gagaga');
};
var Chicken=function () {};
Chicken.prototype.sound=function () {
    console.log('gegege');
};
makeSound(new Duck());//嘎嘎嘎
makeSound(new Chicken());//咯咯咯
```
```
//好处就是以后可以任意追加其他小动物，而不用修改公共代码
var Dog=function(){};
Dog.prototype.sound=function () {
    console.log('wangwangwang');
};
makeSound(new Dog());//wangwangwang
```
#### 封装
封装的目的是将信息隐藏。在JS中，只能依赖变量的作用域来实现封装特性，除了es6提供的let、Symbol外，一般通过自执行函数（IIFE）来创建作用域。这种方式封装的是数据。
封装不仅仅是隐藏数据，还包括隐藏实现细节、设计细节以及隐藏对象的类型等。
#### 原型模式和基于原型继承的JS对象系统
js中常用的6种继承方式，即子类继承父类的方法：
   1. 原型继承 
   子类的原型指向父类的一个实例  父类的私有和公有方法都是子类的公有方法
   ```
   //原型继承
   function A(){
       this.x = 100;
       this.y = 100;
   }
   A.prototype.getX = function (){}
   
   function B(){
       this.x = 100;
   }
   B.prototype = new A(); //B的原型重新赋值A的实例==>每个b实例都能通过自己的__proto__找到a上的所有的私有方法，并且还可以通过a.__proto__继续找到A.prototype上的所有属性 ==> A这个类上的方法相对于B都是公有方法
   B.prototype.constructor = B; //由于重新修改了B的原型导致默认原型上的constructor丢失，我们需要自己添加上
   ```
   2. call继承  
   在子类构造函数中, 父类.call(this)  父类的私有变成子类的私有
    ```
    // call继承
    function C(){
        this.x = 100; //d.x = 100
    }
    C.prototype.getX = function (){};
    function D(){
        C.call(this); //构造函数中的this就是当前实例  this ==> d     C.call(d);  ==> 把C中的this修改成d
    }
    ```
   3. 冒充对象继承 
   循环遍历父类实例，然后父类实例的私有方法全部拿过来添加给子类实例，如果不做hasOwnProperty判断那么就是把父类的公有的和私有的都拿过来当私有的
   ```
   //冒充对象继承
   function E(){
       this.x = 100;
       this.y = 200;
   }
   E.prototype.getX = function (){}
   function F(){
       var e = new E(); //e.x = 100;
       for(var attr in e){ //for in可以遍历到原型上的公有自定义属性
           //attr = x
           if(e.hasOwnProperty(attr)){ //如果这个判断不加，那么无论是公有的还是私有的都拿过来当私有的
               this[attr] = e[attr];//???  f['x'] = e['x'] ==> f.x = 100;
           }
           // e.propertyIsEnumerable() //可枚举属性  ==> 可以拿出来一一列举的属性
       }
   }
   ```
   4. 混合继承  
   原型+call  ==> 无论是私有的还是公有的都拿过来了 ==> 子类的原型上的多了一套父类私有属但是不会产生问题。因为子类的私有属性也有一套相同的通过call继承拿过来的
   ```
   //混合继承
   function G(){
       this.x = 100;
   }
   G.prototype.getX = function(){};
   function H(){
       G.call(this);
   }
   H.prototype = new G();
   H.prototype.constructor = H;
   ```
   5. 中间件继承  
   M.prototype.__proto__ = K.prototype;  把父类的公有的当作子类的公有的
   ```
   // 中间件继承
   function K(){
       this.x = 100;
   }
   k.prototype.getX = function (){};
   function M(){
   
   }
   M.prototype.__proto__ = K.prototype; //本来应该是直接指向Object.prototype。从K的原型上的__proto__也可以到Object.prototype==> 在K.prototype上停留了下，K.prototype就是一个中间件  父类的公有当作子类的公有
   ```
   6. 寄生组合式继承  
   Object.create(xx.prototype)方法  ==> 是把xx.prototype当作别人家的原型  不兼容，只能找到父类原型的方法。父类的私有属性访问不到。
   ```
   function Parent() {
       this.name = "ywx";
   }
   Parent.prototype.eat = function () {
       console.log(123);
   };
   function Child() {}
   //寄生组合式继承实现方式如下：
   Child.prototype = Object.create(Parent.prototype);
   var child=new Child();
   console.log(child.eat());//只有找到父类原型上的方法 
   ```
   注：prototype是函数（一般指构造函数）的一个内置属性;__proto__是一个对象（即实例）拥有的内置属性,是JS内部使用寻找原型链的属性。 实例.__proto__=构造函数.prototype;
   
   
