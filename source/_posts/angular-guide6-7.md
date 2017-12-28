---
title: Angular权威指南学习(第6-7章)表达式和过滤器
date: 2016-05-10 15:09:49
tags: angular、表达式、filter
---
#### 表达式
用\{ \{ \} \}符号将一个变量绑定到$scope上的写法本质上就是一个表达式：`{{expression}}`。当用$watch进行监听时， AngularJS会对表达式或函数进行运算。
表达式和eval(javascript)非常相似，但是由于表达式由AngularJS来处理，它们有以下显著不同的特性：
   1. 所有的表达式都在其所属的作用域内部执行，并有访问本地$scope的权限；
   2. 如果表达式发生了TypeError和ReferenceError并不会抛出异常；
   3. 不允许使用任何流程控制功能（条件控制，例如if/eles）；
   4. 可以接受过滤器和过滤器链。
#### 解析 AngularJS 表达式
AngularJS会在运行$digest循环的过程中自动解析表达式，AngularJS通过$parse这个内部服务来进行表达式的运算，这个服务能够访问当前所处的作用域。这个过程允许我们访问定义在$scope上的原始JavaScript数据和函数。将$parse服务注入到控制器中，然后调用它就可以实现手动解析表达式。
如果页面上有一个输入框绑定到了expr变量上，如下所示:
```
<div ng-controller="MyController">
<input ng-model="expr"
type="text"
placeholder="Enter an expression" />
<h2>{{ parseValue }}</h2>
</div>
我们可以在MyController中给expr这个表达式设置一个$watch并解析它：
angular.module("myApp", [])
.controller('MyController',
function($scope,$parse) {
$scope.$watch('expr', function(newVal, oldVal, scope) {
if (newVal !== oldVal) {
// 用该表达式设置parseFun
var parseFun = $parse(newVal);
// 获取经过解析后表达式的值
$scope.parsedValue = parseFun(scope);
}
});
});
```
注：$watch函数会监视$scope上的某个属性。只要属性发生变化就会调用对应的函数。可以使用$watch函数在$scope上某个属性发生变化时直接运行一个自定义函数。
#### 插值字符串（不常用）
在开发中，我们的确有手动运行模板编译的能力。要在字符串模板中做插值操作，需要在你的对象中注入$interpolate服务。
$interpolate服务返回一个函数，用来在特定的上下文中运算表达式。
#### 过滤器
过滤器用来格式化需要展示给用户的数据。 AngularJS有很多实用的内置过滤器，同时也提供了方便的途径可以自己创建过滤器。在HTML中的模板绑定符号\{ \{ \} \}内通过|符号来调用过滤器。例如，假设我们希望将字符串转换成大写，可以对字符串中的每个字符都单独进行转换操作，也可以使用过滤器：
```
{{ name | uppercase }}
```
在JavaScript代码中可以通过$filter来调用过滤器。例如，在JavaScript代码中使用lowercase过滤器：
```
app.controller('DemoController', ['$scope', '$filter',
function($scope, $filter) {
$scope.name = $filter('lowercase')('WEBYWX');
}]);
```

以HTML的形式使用过滤器时，如果需要传递参数给过滤器，只要在过滤器名字后面加冒号即可。如果有多个参数，可以在每个参数后面都加入冒号。例如，数值过滤器可以限制小数点后的位数，在过滤器后写上:2可以将2作为参数传给过滤器：
```
<!-- 显示： 123.46 -->
{{ 123.456789 | number:2 }}
```
可以用|符号作为分割符来同时使用多个过滤器.
#### angular 内置过滤器
1. currency
   currecy过滤器可以将一个数值格式化为货币格式。用\{ \{ 123 | currency \} \}`来将123转化成货币格式。
2. date
date过滤器可以将日期格式化成需要的格式。 AngularJS中内置了几种日期格式，如果没有指定使用任何格式，默认会采用mediumDate格式，下面的例子中展示了这个格式。下面是内置的支持本地化的日期格式：
```
{{ today | date:'medium' }} <!-- Aug 09, 2013 12:09:02 PM -->
{{ today | date:'short' }} <!-- 8/9/1312:09PM -->
{{ today | date:'fullDate' }} <!-- Thursday, August 09, 2013 -->
{{ today | date:'longDate' }} <!-- August 09, 2013 -->
{{ today | date:'mediumDate' }}<!-- Aug 09, 2013 -->
{{ today | date:'shortDate' }} <!-- 8/9/13 -->
{{ today | date:'mediumTime' }}<!-- 12:09:02 PM -->
{{ today | date:'shortTime' }} <!-- 12:09 PM -->
 年份格式化
四位年份： {{ today | date:'yyyy' }} <!-- 2013 -->
两位年份： {{ today | date:'yy' }} <!-- 13 -->
一位年份： {{ today | date:'y' }} <!-- 2013 -->
 月份格式化
英文月份： {{ today | date:'MMMM' }} <!-- August -->
英文月份简写： {{ today | date:'MMM' }} <!-- Aug -->
数字月份： {{ today |date:'MM' }} <!-- 08 -->
一年中的第几个月份： {{ today |date:'M' }} <!-- 8 -->
 日期格式化
数字日期： {{ today|date:'dd' }} <!-- 09 -->
一个月中的第几天： {{ today | date:'d' }} <!-- 9 -->
英文星期： {{ today | date:'EEEE' }} <!-- Thursday -->
英文星期简写： {{ today | date:'EEE' }} <!-- Thu -->
 小时格式化
24小时制数字小时： {{today|date:'HH'}} <!--00-->
一天中的第几个小时： {{today|date:'H'}} <!--0-->
12小时制数字小时： {{today|date:'hh'}} <!--12-->
上午或下午的第几个小时： {{today|date:'h'}} <!--12-->
 分钟格式化
数字分钟数： {{ today | date:'mm' }} <!-- 09 -->
一个小时中的第几分钟： {{ today | date:'m' }} <!-- 9 -->
 秒数格式化
数字秒数： {{ today | date:'ss' }} <!-- 02 -->
一分钟内的第几秒： {{ today | date:'s' }} <!-- 2 -->
毫秒数： {{ today | date:'.sss' }} <!-- .995 -->
 字符格式化
上下午标识： {{ today | date:'a' }} <!-- AM -->
四位时区标识： {{ today | date:'Z' }} <!--- 0700 -->
下面是一些自定义日期格式的示例：
{{ today | date:'MMMd, y' }} <!-- Aug9, 2013 -->
{{ today | date:'EEEE, d, M' }} <!-- Thursday, 9, 8-->
{{ today | date:'hh:mm:ss.sss' }} <!-- 12:09:02.995 -->
```
3. filter
filter过滤器可以从给定数组中选择一个子集，并将其生成一个新数组返回。这个过滤器通常用来过滤需要进行展示的元素。例如，在做客户端搜索时，可以从一个数组中立刻过滤出所需的结果。这个过滤器的第一个参数可以是字符串、对象或是一个用来从数组中选择元素的函数。
    下面分情况介绍传入不同类型的参数。
    1.  字符串
    返回所有包含这个字符串的元素。如果我们想返回不包含该字符串的元素，在参数前加!符号。
    2.  对象
    AngularJS会将待过滤对象的属性同这个对象中的同名属性进行比较，如果属性值是字符串就会判断是否包含该字符串。如果我们希望对全部属性都进行对比，可以将$当作键名。
    3.  函数
    对每个元素都执行这个函数，返回非假值的元素会出现在新的数组中并返回。
    例如，用下面的过滤器可以选择所有包含字母e的单词：
    ```
    {{ ['Ari','Lerner','Likes','To','Eat','Pizza'] | filter:'e' }}
    <!-- ["Lerner","Likes","Eat"] -->
    ```
    如果要过滤对象，可以使用上面提到的对象过滤器。例如，如果有一个由people对象组成的
    数组，每个对象都含有他们最喜欢吃的食物的列表，那么可以用下面的形式进行过滤：
    ```
    {{ [{
    'name': 'Ari',
    'City': 'San Francisco',
    'favorite food': 'Pizza'
    },{
    'name': 'Nate',
    'City': 'San Francisco',
    'favorite food': 'indian food'
    }] | filter:{'favorite food': 'Pizza'} }}
    <!-- [{"name":"Ari","City":"SanFrancisco","favoritefood":"Pizza"}] -->
    ```
    也可以用自定义函数进行过滤（在这个例子中函数定义在$scope上）：
    ```
    {{ ['Ari','likes','to','travel'] | filter:isCapitalized }}
    <!-- ["Ari"] -->
    isCapitalized函数的功能是根据首字母是否为大写返回true或false，具体如下所示：
    $scope.isCapitalized = function(str) {
    return str[0] == str[0].toUpperCase();
    };
    ```
    我们也可以给filter过滤器传入第二个参数，用来指定预期值同实际值进行比较的方式。
    第二个参数可以是以下三种情况之一。
     true
    用angular.equals(expected, actual)对两个值进行严格比较。
     false
    进行区分大小写的子字符串比较。
     函数
    运行这个函数，如果返回真值就接受这个元素。
#### json
json过滤器可以将一个JSON或JavaScript对象转换成字符串。这种转换对调试非常有帮助：
```
{{ {'name': 'Ari', 'City': 'SanFrancisco'} | json }}
<!--
{
"name": "Ari",
"City": "San Francisco"
}
-->
```
5. limitTo
limitTo过滤器会根据传入的参数生成一个新的数组或字符串，新的数组或字符串的长度取决于传入的参数，通过传入参数的正负值来控制从前面还是从后面开始截取。如果传入的长度值大于被操作数组或字符串的长度，那么整个数组或字符串都会被返回。
例如，我们可以截取字符串的前三个字符：
```
{{ San Francisco is very cloudy | limitTo:3 }}
<!-- San -->
或最后的六个字符：
{{ San Francisco is very cloudy | limitTo:-6 }}
<!-- cloudy -->
对数组也可以进行同样的操作。返回数组的第一个元素：
{{ ['a','b','c','d','e','f'] | limitTo:1 }}
<!-- ["a"] -->
```
6. lowercase
lowercase过滤器将字符串转为小写。
```
{{ "San Francisco is very cloudy" | lowercase }}
<!-- san francisco is very cloudy -->
```
7. number
number过滤器将数字格式化成文本。它的第二个参数是可选的，用来控制小数点后截取的位数。如果传入了一个非数字字符，会返会空字符串。
```
{{ 123456789 | number }}
<!-- 1,234,567,890 -->
{{ 1.234567 | number:2 }}
<!-- 1.23 -->
```
8. orderBy
orderBy过滤器可以用表达式对指定的数组进行排序。orderBy可以接受两个参数，第一个是必需的，第二个是可选的。
第一个参数是用来确定数组排序方向的谓词。
    下面分情况讨论第一个参数的类型。
     函数
    当第一个参数是函数时，该函数会被当作待排序对象的getter方法。
     字符串
    对这个字符串进行解析的结果将决定数组元素的排序方向。我们可以传入+或-来强制进行升序或降序排列。
     数组
    在排序表达式中使用数组元素作为谓词。对于与表达式结果并不严格相等的每个元素，则使用第一个谓词。
    第二个参数用来控制排序的方向（是否逆向）。
    例如，我们将下面的对象数组用name字段进行排序：
    ```
    {{ [{
    'name': 'Ari',
    'status': 'awake'
    },{
    'name': 'Q',
    'status': 'sleeping'
    },{
    'name': 'Nate',
    'status': 'awake'
    }] | orderBy:'name' }}
    <!--
    [
    {"name":"Ari","status":"awake"},
    {"name":"Nate","status":"awake"},
    {"name":"Q","status":"sleeping"}
    ]
    -->
    也可以对排序结果进行反转。例如，通过将第二个参数设置为true可以将排序结果进行反转：
    {{ [{
    'name': 'Ari',
    'status': 'awake'
    },{
    'name': 'Q',
    'status': 'sleeping'
    },{
    'name': 'Nate',
    'status': 'awake'
    }] | orderBy:'name':true }}
    <!--
    [
    {"name":"Q","status":"sleeping"},
    {"name":"Nate","status":"awake"},
    {"name":"Ari","status":"awake"}
    ]
    -->
    ```
9. uppercase
uppercase过滤器可以将字符串转换为大写形式：
```
{{ "San Francisco is very cloudy" | uppercase }}
<!-- SAN FRANCISCO IS VERY CLOUDY -->
```
#### 自定义过滤器
创建自定义过滤器非常容易。创建自定义过滤器需要将它放到自己的模块中。
例：实现一个过滤器，将字符串第一个字母转换为大写
首先，创建一个模块用以在应用中进行引用（这是一个非常好的实践）：
```
angular.module('myApp.filters', [])
.filter('capitalize', function() {
return function(input) {};
});
```
过滤器本质上是一个会把我们输入的内容当作参数传入进去的函数。上面这个例子中，我们在调用过滤器时简单地把input当作字符串来处理。可以在这个函数中做一些错误检查：
```
angular.module('myApp.filters', [])
.filter('capitalize', function() {
return function(input) {
// input是我们传入的字符串
if (input) {
return input[0].toUpperCase() + input.slice(1);
}
});
```
现在，如果想将一个句子的首字母转换成大写形式，可以用过滤器先将整个句子都转换成小写，再把首字母转换成大写：
```
<!-- Ginger loves dog treats -->
{{ 'ginger loves dog treats' | lowercase | capitalize }}
```
#### 表单验证
AngularJS能够将HTML5表单验证功能同它自己的验证指令结合起来使用，并且非常方便。
```
<form name="form" novalidate>
<label name="email">Your email</label>
<input type="email"
name="email"
ng-model="email" placeholder="Email Address" />
</form>
```
注：要使用表单验证，首先要确保表单像上面的例子一样有一个name属性。
所有输入字段都可以进行基本的验证，比如最大、最小长度等。这些功能是由新的HTML5表单属性提供的。如果想要屏蔽浏览器对表单的默认验证行为，可以在表单元素上添加novalidate标记。
   - input元素所有验证选项如下：
   1. 必填项
   验证某个表单输入是否已填写，只要在输入字段元素上添加HTML5标记required即可：
   ```
   <input type="text" required />
   ```
   2. 最小长度
   验证表单输入的文本长度是否大于某个最小值，在输入字段上使用AngularJS指令ng-minleng="{number}"：
   ```
   <input type="text" ng-minlength="5" />
   ```
   3. 最大长度
   验证表单输入的文本长度是否小于或等于某个最大值，在输入字段上使用AngularJS指令ng-maxlength="{number}"：
   ```
   <input type="text" ng-maxlength="20" />
   ```
   4. 模式匹配
   使用ng-pattern="/PATTERN/"来确保输入能够匹配指定的正则表达式：
   ```
   <input type="text" ng-pattern="[a-zA-Z]" />
   ```
   5. 电子邮件
   验证输入内容是否是电子邮件，只要像下面这样将input的类型设置为email即可：
   ```
   <input type="email" name="email" ng-model="user.email" />
   ```
   6. 数字
   验证输入内容是否是数字，将input的类型设置为number：
   ```
   <input type="number" name="age" ng-model="user.age" />
   ```
   7. URL
   验证输入内容是否是URL，将input的类型设置为 url：
   ```
   <input type="url" name="homepage" ng-model="user.facebook_url" />
   ```
   8. 在表单中控制变量
   表单的属性可以在其所属的$scope对象中访问到，而我们又可以访问$scope对象，因此JavaScript可以间接地访问DOM中的表单属性。借助这些属性，我们可以对表单做出实时（和AngularJS中其他东西一样）响应。
   注：可以使用下面的格式访问这些属性。
   formName.inputFieldName.property
    未修改的表单
   这是一个布尔属性，用来判断用户是否修改了表单。如果未修改，值为true，如果修改过值
   为false：
   formName.inputFieldName.$pristine
    修改过的表单
   只要用户修改过表单，无论输入是否通过验证，该值都返回true：
   formName.inputFieldName.$dirty
    合法的表单
   这个布尔型的属性用来判断表单的内容是否合法。如果当前表单内容是合法的，下面属性的
   值就是true：
   formName.inputFieldName.$valid
    不合法的表单
   这个布尔属性用来判断表单的内容是否不合法。如果当前表单内容是不合法的，下面属性的
   值为true：
   formName.inputFieldName.$invalid
    错误
   这是AngularJS提供的另外一个非常有用的属性： $error对象。它包含当前表单的所有验证
   内容，以及它们是否合法的信息。用下面的语法访问这个属性：
   formName.inputfieldName.$error
   如果验证失败，这个属性的值为true；如果值为false，说明输入字段的值通过了验证。
   ```
   例：判断错误提示框是否显示：
   ng-show="signup_form.name.$dirty && signup_form.name.$invalid"
   signup_form为表单的name属性值，name为input输入框的name属性值。
   ```
   9. 一些有用的CSS样式
   AngularJS处理表单时，会根据表单当前的状态添加一些CSS类（例如当前是合法的、未发生变化的，等等），这些CSS类的命名和前面介绍的属性很相似。
   ```
   .ng-pristine {}
   .ng-dirty {}
   .ng-valid {}
   .ng-invalid {}
   ```
   它们对应着表单输入字段的特定状态。
   当某个字段中的输入非法时， .ng-invlid类会被添加到这个字段上。当前例子中的站点将对应的CSS样式设置为：
   ```
   input.ng-invalid {
   border: 1px solid red;
   }
   input.ng-valid {
   border: 1px solid green;
   } 
   ```
   - $parsers
  使用$parsers数组是实现自定义验证的途径之一。
  - $formatters
注：表单本身会提供$invalid和$valid属性。例：formName.$invalid;当用户试图提交表单时，你可以在作用域中捕获到一个submitted值，然后对表单内容进行验证并显示错误信息。一般当formName.submitted设置为true时，容纳错误信息的div才会展示出来。
#### 从1.3开始， Angular中新增了一个ngMessages指令。
   1. 安装ngMessages很简单，因为它被打包成了一个Angular模块。首先下载这个模块：
```
$ bower install --save angular-messages
```
   2. 还需要将angular-messages.js这个JavaScript引入我们的主HTML中：
   ```
<script type="text/javascript" src="bower_components/angular-messages/angular-messages.js">
</script>
```
   3. 最后，我们还要告诉Angular将ngMessages作为应用程序的依赖模块引入，就像这样：
   ```
angular.module('myApp', ['ngMessages']);
```
  例：在没有使用ngMessages之前，我们可能这样写验证：
  ```
  <form name="userForm">
  <input 
  type="text"
  name="username"
  ng-model="user.username"
  ng-minlength="3"
  ng-maxlength="8"
  required>
  <p ng-show="userForm.username.$error.minlength">Username is too short.</p>
  <p ng-show="userForm.username.$error.maxlength">Username is too long.</p>
  <p ng-show="userForm.username.$error.required">Your username is required.</p>
  </form>
  ```
  有了"ngMessages"这个module,大致这么写：
  ```
  <div class="help-block" ng-messages="userForm.name.$error" ng-if="userForm.name.$touched">
  <p ng-message="minlength">用户名最小长度5</p>
  <p ng-message="maxlength">用户名最大长度10</p>
  <p ng-message="required">用户名必填</p>
  </div>
  ```
  ngMessages为我们自动判断显示哪种错误。
  
####  使用ngMessages的几个关键点：
  
   1. ● npm install angular-messages
   2. ● 引用：angular-messages.js
   3. ● 依赖：angular.module('app',['ngMessages'])

