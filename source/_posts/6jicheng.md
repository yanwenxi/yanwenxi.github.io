---
title: js学习--6种继承方式
date: 2015-10-15 14:12:39
tags: js继承
---
js中常用的继承方式: 子类继承父类的方法：
### 1、原型继承  
子类的原型指向父类的一个实例  父类的私有和公有方法都是子类的公有方法
```
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
### 2、call继承  
在子类构造函数中 父类.call(this)  父类的私有变成子类的私有
```
function C(){
    this.x = 100; //d.x = 100
}
C.prototype.getX = function (){};
function D(){
    C.call(this); //构造函数中的this就是当前实例  this ==> d     C.call(d);  ==> 把C中的this修改成d
}
```
### 3、冒充对象继承 
循环遍历父类实例，然后父类实例的私有方法全部拿过来添加给子类实，如果不做hasOwnProperty判断那么就是把父类的公有的和私有的都拿过来当私有的
```
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
            this[attr] = e[attr];
            //f['x'] = e['x'] ==> f.x = 100;
        }
        // e.propertyIsEnumerable() //可枚举属性  ==> 简单理解：可以拿出来一一列举的属性
    }
}
```
### 4 混合继承  
原型+call  ==> 无论是私有的还是公有的都拿过来了 ==> 子类的原型上的多了一套父类私有属但是不会产生问题。因为子类的私有属性也有一套相同的通过call继承拿过来的
```
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
###  5 中间件继承  
M.prototype.__proto__ = K.prototype;  把父类的公有的当作子类的公有的
```
function K(){
    this.x = 100;
}
k.prototype.getX = function (){};
function M(){

}
M.prototype.__proto__ = K.prototype; //本来应该是直接指向Object.prototype。从K的原型上的__proto__也可以到Object.prototype==> 在K.prototype上停留了下，K.prototype就是一个中间件  父类的公有当作子类的公有
```
### 6 寄生组合式继承 
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
