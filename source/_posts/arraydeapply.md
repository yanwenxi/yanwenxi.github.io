---
title: js 数组中自定义属性的应用
date: 2016-11-13 17:18:52
tags: js Array
---
首先要说明的是：数组也是一个object,所以也可以添自定义属性。
```
var arr = [];即可表示一个完整的数组
console.log(arr.length) //0
```
就是说数组本身是有属性的，这样一来，我们就可以添加自己需要的属性了
```
arr.load = false;//查看load
console.log(arr.load);//false
arr.load = true; //修改load的值
console.log(arr.load);//true
```
有人会说，这有什么用？就是一个自定义属性而已。请看下面内容便知道了。

前端在通过ajax获取后台数据时，列表通常返回数组形式
前台展示列表：
```
//默认值
var showList = [];
ajax获取值：
//获取前，页面会提示正在获取数据
ajax(url,function(data){
    showList = data.list;
    //获取到数据后，会返回这个list,此时，如果list是空的，那么，showList就没有发生变化
});
```
当返回为空时，那么该如何通过判断showList.length的变化来修改页面提示信息呢？（正在获取数据…/暂无数据）,此时就需要给showList添加一个自定义属性load,
```
var showList = [];
showList.load = false;
ajax(url,function(data){
    showList.load = true;
    showList = data.list;
});
```
前台就可以根据load状态进行判断提示了（showList.load?’正在获取数据…':’暂无数据’）

技巧虽小，但是有时候可以帮大忙。