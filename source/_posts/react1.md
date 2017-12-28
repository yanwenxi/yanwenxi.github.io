---
title: react入门学习
date: 2016-8-12 22:10:58
tags: react
---
## 1.什么是react
React 是一个用于构建用户界面的JavaScript库
### 2.安装react
`$ bower install react babel --save`
## 3.直接在浏览器中使用React
```
  <script src="../bower_components/react/react.js"></script>
  <script src="../bower_components/react/react-dom.js"></script>
  <script src="../bower_components/babel/browser.js"></script>
  <script type="text/babel" src="index.js"></script>
```
* react.js 是 React 的核心库
* react-dom.js 是提供与DOM相关的功能,会在window下增加ReactDOM属性
* browser.js 的作用是将JSX语法转为JavaScript语法
script中的type属性为text/babel,因为React独有的JSX语法,跟JavaScript不兼容
### 4.ReactDOM.render
ReactDOM.render 是 React 的最基本方法,用于将标签模板转为HTML语言，并插入指定的DOM节点
* 4.1.index.html
  ```
   <div id="app"></div>
   <script type="text/babel" src="js/1.react.js"/>
  ```
- 4.2. js/1.react.js
```
    ReactDOM.render(
    <h1>ywx</h1>,
        document.getElementById('app')
    );
```
上面代码将一个h1标题，插入app元素内部
## 5.JSX 语法
 它是一种JS和HTML混合的语法,将组件的结构、数据甚至样式都聚合在一起定义组件,会编译成普通的Javascript。
* 遇到HTML标签(以 < 开头)，就用HTML规则解析
* 遇到代码块(以 { 开头)，就用JavaScript规则解析
* 使用样式时可以让style等于一个样式对象
* 使用样式类时只能使用className=类名,因为class是Javascript关键字
```
var persons = ['刘德华', '范冰冰', '郭跃'];
var style = {color:'red'};
ReactDOM.render(
  <div>
  {
    persons.map(function (person) {
      return <div style={style}>Hello, {person}!</div>
    })
  }
  </div>,
  document.getElementById('app')
);
```
### 6.定义组件
我们可以很直观的将一个复杂的页面分割成若干个独立组件,每个组件包含自己的逻辑和样式 再将这些独立组件组合完成一个复杂的页面。 这样既减少了逻辑复杂度，又实现了代码的重用
•	可组合：一个组件可以和其他的组件一起使用或者可以直接嵌套在另一个组件内部
•	可重用：每个组件都是具有独立功能的，它可以被使用在多个场景中
•	可维护：每个小的组件仅仅包含自身的逻辑，更容易被理解和维护
 - 6.1 定义组件
React允许将代码封装成组件，然后像插入普通HTML标签一样，在网页中插入这个组件
* 组件类的第一个字母必须大写
* 组件类能且只能包含一个顶层标签
```
var Message = React.createClass({
    render: function() {
        return <h1>Hello</h1>;
    }
});
ReactDOM.render(
    <Message/>,
    document.getElementById('app')
);
```
 - 6.2 组件的属性
* 每个组件可以有自己的属性,一般用来存放组件初始后不变的数据,比如人的性别，姓名等
* 属性一般用作组件的数据源，一般由父组件传入,比如你的名字一般是由你父母取的
* 属性可以通过this.props中取出
* propTypes可以用来定义传入组件属性的名称和类型
* getDefaultProps函数可以用来定会引起组件的默认属性
```
var Person = React.createClass({
    //类似于约定了一个接口文档,用于这是验证传递给组件的属性，
    propTypes: {
        //定义msg的属性类型为字符串，必须传入
        name: React.PropTypes.string.isRequired,
        gender: React.PropTypes.string.isRequired,
        age:React.PropTypes.number.isRequired
    },
    getDefaultProps:function(){
        return {name:'无名氏'}
    },
    render: function() {
        //属性可以通过属性对象this.props中取出
        return (<h1> {this.props.name}
                     {this.props.gender}
                     {this.props.age}
                </h1>);
    }
});

var props = {
    gender:'男',
    age:18
}

ReactDOM.render(
    <Person {...props} />,//属性可以在使用组件时传入
    document.getElementById('app')
);
```
