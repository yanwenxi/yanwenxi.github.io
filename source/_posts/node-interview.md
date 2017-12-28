---
title: Node.js 面试问题及答案
date: 2017-01-11 17:21:52
tags: nodejs
---
#### Node.js 面试问题预览(来源于网络)
- 什么是 error-first 回调模式？
- 如何避免“回调地狱”？
- 什么是 Promises？
- 什么工具统一团队的代码风格？为什么统一的代码风格很重要？
- 什么时候应当用 npm？什么时候应当用 yarn？
- 什么是桩代码（stub）？ 请描述一个应用场景！
- 什么是测试金字塔？请举例说明！
- 你最欣赏的 HTTP 框架是什么？为什么？
- 如果保证你的 cookie 安全？如何阻止 XSS 攻击？
- 如何确认项目的相关依赖安全？
#### 什么是 error-first 回调模式？
应用 error-first 回调模式是为了更好的进行错误和数据的传递。第一个参数保留给一个错误 error 对象，一旦出错，错误将通过第一个参数 error 返回。其余的参数将用作数据的传递。
```
fs.readFile(filePath,function (err,data) {
    if(err){
        //handle the error code.
        return console.log(err);
    }
    console.log(data);
});
```

#### 如何避免“回调地狱”?
解决这个问题的方法有很多:

- 模块化设计: 讲回调拆分成几个独立的函数
- 使用 流程控制库, 比如 async
- 组合使用 generators 和 Promises
- 使用 async/await 函数 (只能在Node v7 release版本运行， Node LTS版本尚不支持 )

#### 什么是 Promises?
promise的概念早在上个世纪八十年代就被提出，现在已经是大多数现代编程语言中支持的特性，让你能更轻松地实现异步模型。

举个简单的例子, 正常来说100ms 之后将会输出 result 。一旦失败, catch可以抛出异常。Promises允许链式操作。
```
new Promise(()=>{
    setTimeout(()=>{
        resolve('result')
    },100)})
    .then((data)=>{
        console.log(data);
    })
    .catch();
```
#### 什么工具统一团队的代码风格? 为什么统一的代码风格很重要？

团队协作时，始终如一的代码风格十分重要，代码风格一致，团队成员可以更轻松的构建项目，不用为每次构建项目都使用新的代码风格而担心。
同时, 始终如一的代码风格，可以通过静态分析排除代码问题。

帮助规范代码的工具: 1.ESLint 2.Standard
如果你想更上一层楼，可以学习 JavaScript Clean Coding 原则。

#### 什么时候应当用 npm？什么时候应当用 yarn？
相同点：都是javascript包管理器。
yarn 解决了npm历史遗留下来的痛点。
yarn特点：多平台精确校准，只要将yarn.lock保存到源码库里，在任何地方用yarn安装，版本都不会偏差。从而避免了package.json原来经常出现小版本差异而导致程序无法正常运行的情况。
缓存已经下载过的包，避免重复下载
下载前会检查签名及包的完整性
可靠可确定性，保证各平台依赖的一致性
网络优化，力求网络资源最大利用化，让资源下载完美队列执行，避免大量的无用请求，下载失败会自动重新请求，避免整个安装过程失败
扁平化模式，对于不匹配的依赖版本的包创立一个独立的包，避免创建重复的。（可以做个测试当时加载某个项目 用了86秒，后面又用npm测试，用了 190秒，之后在用yarn.lock 的情况下，加载用了46秒）

#### 什么是桩代码（stub）? 请描述一个应用场景！
桩代码（stub）就是在某些组件或模块中，模拟某些功能的代码。桩代码（stub）的作用是占位，让代码在测试过程中顺利运行。

一个例子，它实际的作用是写一个文件，但是这段代码并没有真正的做这件事。
```
var fs = require('fs');
var writeFileStub = sinon.stub(fs,'writeFile',function (path,data,cb) {
    return cb(null);
});
expect(writeFileStub).to.be.called
writeFileStub.restore();
```
#### 什么是测试金字塔？请举例说明！
测试金字塔描述了单元测试（unit test），集成测试（integration tests），端到端测试（end-to-end test）在测试中占的比例。
<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/testjinzita.jpg-public)
</div>

举个例子，测试一个 HTTP API 需要:
1.大量关于 models 的单元测试(使用桩代码处理),
2.一些关于 models 如何和其他 models 交互的集成测试 (未使用桩代码处理),
3.少量的端到端测试，也就是真实环境下的调用 ( 未使用桩代码处理).

#### 你最欣赏的 HTTP 框架是什么？为什么？
这道题没有标准答案。出这道题的目的是，看看面试者对他所使用框架，理解的有多深。只要面试者回答出框架的优缺点，为什么使用这个框架就可以了。

#### 什么时候应该在后台进程中使用消息服务？怎么处理工作线程的任务/怎么给 worker 安排任务？
消息队列适用于后台数据传输服务，比如发送邮件和数据图像处理。

消息队列有很多解决方案，比如 RabbitMQ 和 Kafka.

#### 如何保证你的 cookie 安全？如何阻止 XSS 攻击？

XSS 攻击是指攻击者向Html页面里插入恶意 JavaScript 代码。

为了防止攻击，你需要对 HTTP header 里的 set-cookie 进行处理:
1、HttpOnly - 这个属性帮助防止跨站脚本攻击，它禁止通过 JavaScript 访问 cookie。
2、secure - 这个属性告诉浏览器，仅允许通过 HTTPS 协议访问 cookie。

所以，你需要做的是在请求头里写 Set-Cookie: sid=; HttpOnly。如果你正在使用 Express 框架，可以使用 express-cookie session，他会默认做出上述防止 XSS 攻击的设置。

#### 如何确认项目的相关依赖安全？
使用 Node.js 开发, 很容易出现有成百上千个依赖的情况。

举例来说，如果你依赖 Express，准确的说这意味着也依赖 27 个其他的模块 , 手动检测更新这些模块不是一个正确的选择！

唯一的选择就是自动的安全的更新你的依赖，你有如下免费或付费的选择：
1、pm outdated
2、race by RisingStack
3、SP
4、reenKeeper
5、nyk
#### 下面的代码有什么错误？
```
new Promise((resolve,reject)=>{
    throw new Error('error')
}).then(console.log)
//在 then 后没有 catch ，没有捕捉异常。这样做会造成故障沉默，不会抛出异常。
//如果你调试一个巨大的代码库，并且不知道哪个 Promise 函数有潜在的问题, 你可以使用 unhandledRejection 这个工具。它将会打印出所有未处理的 reject 状态的 Promise。
process.on('unhandleRejection',(err)=>{
    console.log(err);
})
```
```
function checkApiKey(apiKeyFromDb,apiKeyReceived) {
    if(apiKeyFromDb===apiKeyReceived){
        return true;
    }
    return false;
}
//在进行数据校验(security credentials)时，避免任何信息泄露是最重要的。所以，我们要控制数据校验的执行时间。我们要保证，不管传过来的数据是什么，我们校验数据消耗的时间是相同的。如果你做不到这一点，你的程序对时序攻击的抵抗力很低。

为什么会有这种现象?
Node.js 使用 JavaScript V8 引擎, 为了高速运行网页而开发的，性能优异。 V8 引擎比较数据的方式是字节比较, 一旦发现有一个字节不一致, 比较运算就会停止。 因此，攻击者传入的 password 校验时间越长，说明明 password 正确的部分越多。
为了修复这个问题, 你可以使用 npm 模块 cryptiles。

function checkApiKey(apiKeyFromDb,apiKeyReceived) {
    return cryptiles.fixedTimeComparison(apiKeyFromDb,apiKeyReceived)
}
```
```
下面这段代码输出的是什么?
Promise.resolve(1)
    .then((x)=>x+1)
    .then((x)=>{throw new Error('My Error')})
    .catch(()=>1)
    .then((x)=>x+1)
    .then((x)=>console.log(x))
    .catch(console.error)
答案是 2. 每行代码对应解释如下：
创建一个 Promise 对象，输出 1
创建一个 Promise 对象，输出 1
结果 +1，现在的输出为2
输出结果被抛弃，抛出一个 error
error 被丢弃, 返回一个新的 value (1)
catch 进行异常处理，但 catch 后程序不停止执行, 返回一个信息+1之后的 value (2)
value 正常输出
value 已正常输出，这一行不会执行
```