---
title: 杂七乱八小知识点
date: 2015-11-13 16:01:03
tags: 
---
### 1、io.js
io.js 是基于 V8 引擎的较新版本构建的。通过持续跟进最新版的 V8 引擎，我们可以保证及时地为开发者带来最新的 JavaScript ECMA规范 中的语言特性，同时也能得到性能和稳定性的提升。
- 干掉 --harmony(第一种方式)
   node koa-app.js --harmy   可以安装iojs,  用iojs koa-app.js   这样就不用写--harmy
注：--harmy为和谐模式，意思是让node程序启动时兼容ECMA的新特性。
- 第二种方式
alias node='node --harmony'   alias：别名，化名
在chrome中使用该特性需要先打开chrome://flags/, 搜索harmony, enable之, 重启chrome即可
### 2、npm ls
在当前项目下可以查看项目下哪些模块没有安装成功。 npm ls -g  查看全局安装了哪些模块，同理 bower ls可以查看bower管理的包哪些安装成功与否！
### 经过测试IE6,7,8,9均不支持严格模式（"use strict"）。ie10在内的主流浏览器才支持.
### 同构JavaScript应用
指的是用JavaScript编写的应用，能够同时运行于客户端和服务器。这也让在客户端和服务器之间共享代码变成一种更普遍自然的选择。
### 屏幕扩展，多屏开发
### 伪类与伪元素
伪类，就类似class这样，表示一些元素的状态的，但是无须标识的分类。例：a:hover;

伪元素，就是一个元素，类似p标签元素，等等，逻辑上存在，但是实际并不存在在文档树中。

伪类前面是一个冒号，伪元素前面是两个冒号。E:first-child 伪类，E::first-line为元素。
::before 和 ::after 伪元素，before 和after是在元素前面和后面添加一些元素，因此是伪元素，为了区分伪类，CSS3选择器中已经将其前面变为两个冒号了。伪元素因为不在DOM里存在，也不会在调试工具里显示，所以不太好调试
*在a标签hover状态下加一个小三角*，可用伪元素实现.
```
a:hover::after{}
```
### 软件应用版本代号
- alpha版：内部测试版。α是希腊字母的第一个，表示最早的版本，一般用户不要下载这个版本，这个版本包含很多BUG，功能也不全，主要是给开发人员和 测试人员测试和找BUG用的。

- beta版：公开测试版。β是希腊字母的第二个，顾名思义，这个版本比alpha版发布得晚一些，主要是给“部落”用户和忠实用户测试用的，该版本任然存 在很多BUG，但是相对alpha版要稳定一些。这个阶段版本的软件还会不断增加新功能。如果你是发烧友，可以下载这个版本。

- rc版：全写：Release Candidate（候选版本），该版本又较beta版更进一步了，该版本功能不再增加，和最终发布版功能一样。这个版本有点像最终发行版之前的一个类似 预览版，这个的发布就标明离最终发行版不远了。作为普通用户，如果你很急着用这个软件的话，也可以下载这个版本。

- stable版：稳定版。在开源软件中，都有stable版，这个就是开源软件的最终发行版，用户可以放心大胆的用了。
### 将类数组对象转换成数组
扩展运算符可以将一个类数组对象中索引范围在[0,length)之间的所有属性的值添加到一个数组中,这样就可以得到一个真正的数组:
```
var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
```
当然也可以用es6的新方法：
```
var array=Array.from(nodeList);
```
### assert断言库与钩子函数
assert断言用于在开发阶段监测BUG，进行调试。其存在的意义在于检测代码在开发过程中是否出现了问题。
*钩子函数*：hook function  钩子的本质是一段用以处理系统消息的程序，通过系统调用，把它挂入系统。
React中可以指定在组件的生命周期的不同阶段执行的函数，这些函数就是钩子函数。例：componentDidMount、shouldComponentUpdate等等
### github pull request 贡献代码
假设有一个仓库，叫Repo A。你如果要往里贡献代码，首先要Fork这个Repo，于是在你的Github账号下有了一个Repo A2,。然后你在这个A2下工作，Commit，push等。然后你希望原始仓库Repo A合并你的工作，你可以在Github上发起一个Pull Request，意思是请求Repo A的所有者从你的A2合并分支。如果被审核通过并正式合并，这样你就为项目A做贡献了
Github pull request 简写github pr 就是『请求代码合并』。
### lodash
lodash 是一个具有一致接口、模块化、高性能等特性的 JavaScript 工具库。
```
_.assign({ 'a': 1 }, { 'b': 2 }, { 'c': 3 });
// → { 'a': 1, 'b': 2, 'c': 3 }
_.map([1, 2, 3], function(n) { return n * 3; });
// → [3, 6, 9]
```
### 箭头函数的使用注意点：
1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。

2. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。

3. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替。

4. 不可以使用yield命令，因此箭头函数不能用作Generator函数。

上面四点中，第一点尤其值得注意。this对象的指向是可变的，但是在箭头函数中，它是固定的。
### 判断一个函数是否是generatorFunction的方法
```
function isGeneratorFunction(obj) {
  return obj && obj.constructor && 'GeneratorFunction' === obj.constructor.name
}
```
### ES7语法(async,await)
koa用generator这种处理异步方式估计会被es7里的async function 取代， 转义ES7语法(async,await)， 需要stage-3规则，stage-3规则是ES7的stage 0~3的第3阶段规则。 node现在还不支持es7。
### 函数绑定（::）
　　箭头函数可以绑定this对象，大大减少了显式绑定this对象的写法（call、apply、bind）。但是，箭头函数并不适用于所有场合，所以ES7提出了“函数绑定”（function bind）运算符，用来取代call、apply、bind调用。虽然该语法还是ES7的一个提案，但是Babel转码器已经支持。

　　函数绑定运算符是并排的两个双冒号（::），双冒号左边是一个对象，右边是一个函数。该运算符会自动将左边的对象，作为上下文环境（即this对象），绑定到右边的函数上面。
### jq中的回调
　　$.ajax()操作完成后，如果使用的是低于1.5.0版本的jQuery，返回的是XHR对象，你没法进行链式操作；如果高于1.5.0版本，返回的是deferred对象，可以进行链式操作。deferred对象就是jQuery的回调函数解决方案
### TypeScript
　　TypeScript 是 JavaScript 的超集，扩展了 JavaScript 的语法，因此现有的 JavaScript 代码可与 TypeScript 一起工作无需任何修改，*TypeScript 通过类型注解提供编译时的静态类型检查*。TypeScript 可处理已有的 JavaScript 代码，并只对其中的 TypeScript 代码进行编译。
### prcoess
  代码中用此取值：prcoess.env.PORT         控制台设置：set PORT=8080
  prcoess.env.NODE_ENV    set NODE_ENV=production
### hostAdmin
   若C:\Windows\System32\drivers\etc正常访问能找到hosts文件，用hostAdmin访问不到etc这个文件夹，说明hostadmin没有权限访问，此时etc右击属性-共享里设置，将此文件的访问权限授权给everyone.这样就hostadmin能访问此文件夹了.
### AngularJS 项目中如何实现按需加载js文件
requirejs实现angular js文件按需加载,RequireJS + AngularJS实施.
#### 链式调用
```
ABC.prototype.add=function(){
....//some code
return this; //实现链式调用的原理
}
```


