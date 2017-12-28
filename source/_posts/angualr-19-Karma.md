---
title: Angular中的测试-Karma
date: 2016-08-12 20:34:29
tags: angular、Karma
---
#### AngularJS 测试的类型
   1. 单元测试
    我们可以专注于构建我们的测试来隔离具体、单独组件的代码。这种方法被称为“单元测试”，在不同阶段、不同条件下、以不同的输入来测试各种特定单元的代码。
    单元测试专门用于测试小型、独立的代码单元，单个函数，或者较小的带有功能的交互。它不是用来测试大功能集的。
    单元测试的麻烦在于把逻辑隔离成小块，这样我们才能测试它.
   2. 端到端测试
    另一方面，也可以对我们的应用作黑盒测试（也就是端到端测试）。在端到端（E2E）测试中，我们测试应用的视角是：作为最终用户，对系统的底层实现一无所知。这种方法非常适合测 试大型应用的功能。端到端测试适合测试页面上的用户交互，无需手动刷新页面。
    关于这类测试的介绍并不新鲜，还有些了不起的工具，能使我们建立自动化的浏览器测试。我们可以使用工具如PhantomJS或CasperJS来进行无头浏览器测试（即不必打开浏览器），或Karma等工具，会真正将打开一个浏览器并在一个iframe中执行所有的测试。
#### Karma
Karma测试运行器同时支持单元测试和端到端测试
要运行我们的测试，需要安装Karma测试运行器。
```
$ npm install -g karma
```
运行一个Karma测试挺容易的： karma start path/to/karma.config.js(例：test/karma.config.js)。当测试运行器启
动的时候，它会把在Karma配置文件中列出的浏览器启动起来，默认情况下，如果不是另有规定， Karma将监控配置文件中列出的所有文件。任何时候有文产生了变化， Karma都将运行适当的测试。
#### 初始化 Karma 配置文件
Karma给了我们一个生成器来创建配置文件。这个生成器会问几个关于要怎样建立配置文件的问题，每个问题建议了一个默认值，可以简单地接受所有默认值即可。用karma init生成器来创建karma.conf.js配置文件。
   1. 建立单元测试
   在测试文件的路径运行karma init命令，在我的例子中，在tests目录中创建Karma配置文件：
   ```
   $ karma init test/karma.conf.js
   ```
   对于单元测试而言，运行测试所需的依赖项都要具备。当使用Karma生成器来构建单元测试 时，单元测试包含下列这些代码的引用是很重要的：
   一、 一个测试框架（选一个）1、Jasmine（默认）2、Mocha  3、QUnit。二、自定义的配置文件（需要w/Mocha）三、所需的第三方代码。
   四、应用特有的代码。五、测试代码。六、模拟用的angular-mock.js库。
    单元测试需要引用待测试应用的所有代码，也要引用所有要写的测试代码。
   #### 例如，一个示例单元测试的Karma配置文件可能看起来像下面这样：
    ```
    module.exports = function(config) {
    config.set({
    basePath: '..',
    frameworks: ['jasmine'],
    files: [
    'lib/angular.js',
    'lib/angular-route.js',
    'test/lib/angular-mocks.js',
    'js/**/*.js',
    'test/unit/**/*.js'
    ],
    exclude: [],
    port: 8080,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    browsers: ['Safari'],
    singleRun: false
    });
    };
    ```
   设置了这个文件之后，就能像下面这样运行单元测试了：
    ```
    $ karma run test/karma.conf.js
    ```
   另外，如果想在每次代码有变化时运行单元测试（如果把autoWatch设置成了true） ，可以像下面这样：
    ```
    $ karma start test/karma.conf.js
    ```
   2. 建立端到端测试
   要设置端到端测试，要使用端到端测试Karma配置文件的路径来运行Karma生成器。
   ```
   $ karma init test/karma-e2e.conf.js
   ```
   端到端测试要使用ng-scenario框架。不像单元测试那样，我们不需要引用所有的库代码：端到端测试是跑在服务器上的， ng-scenario框架只   需要在浏览器中加载所有这些测试就可以了。
   ##### 端到端测试的示例Karma配置文件可能长这样：
   ```
   module.exports = function(config) {
   config.set({
   basePath: '..',
   frameworks: ['ng-scenario'],
   files: [
   'test/e2e/**/*.js'
   ],
   exclude: [],
   port: 8080,
   logLevel: config.LOG_INFO,
   autoWatch: false,
   browsers: ['Chrome'],
   singleRun: false,
   urlRoot: '/_karma_/',
   proxies: {
   '/': 'http://localhost:9000/'
   }
   });
   };
   ```
   设置好这个配置后，就可以这样运行端到端测试：
   ```
   $ karma run test/karma-e2e.conf.js
   ```
   如果想要在每次代码有变化时运行我们的测试（如果把autoWatch设置成了true)同上。
#### 配置选项
   1. 框架
   生成器会问我们使用哪个测试框架来做测试。 Jasmine是默认的测试框架，不过，生成器默认也支持Mocha、 QUnit和其他测试框架。
   这些测试框架都需要安装额外的npm库。例如，要使用Jasmine框架，需要安装Jasmine插件。
   ```
   $ npm install --save-dev karma-jasmine
   ```
   然后设置`frameworks: ['jasmine']`;
   2. RequireJS
   如果这个项目使用了RequireJS库，询问是否要包含RequireJS的，就要回答yes。如果项目没有包含它，就选no,不是把项目中所有文件都列在Karma配置文件中，而是要包含单独的测试文件，它负责加载特定模块。
   3. 浏览器捕获
   Karma生成器会询问要自动启动哪个浏览器来捕获测试结果。终止测试运行器时， Karma也会把这些浏览器关掉。我们也可以用浏览器打开Karma Web服务器监听的URL（默认为http://localhost:9876 ）来测试，如果要从本地网络的另一台机器（或者虚拟机）的IE来测试的话，
   这一点值得牢记。
   要使用Karma来启动和运行，每个浏览器都需要安装额外的插件。我们可以使用npm来安装这些插件。例如，为了让Karma控制Chrome，我们就要安装Chrome启动插件。
   ```
   $ npm install --save-dev karma-chrome-launcher
   ```
   如果要使用Safari，就要安装Safari启动插件，对于Firefox，就要安装Firefox启动插件，其他类同。
   ```
   browsers: ['Chrome', 'Safari']
   ```
测试指令也比测试控制器容易得多。指令并不一定要有视图模板。通常情况下，它们可以只作为视图之下处理数据的垫片。ngModelController控制器就是这种功能派上用场的一个例子。
   其他配置可以按字面意思猜测出来了，更多案例详细讲解请参照angular权威指南19章。

