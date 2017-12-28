---
title: Angular权威指南学习（第8到9章）指令
date: 2016-05-13 21:09:01
tags: angular、directive
---
#### 指令
指令本质上就是AngularJS扩展具有自定义功能的HTML元素的途径。例如，我们可以创建一个自定义元素，它实现了`<video>`标签的功能并且能在所有浏览器中工作：

```
<my-better-video my-href="/goofy-video.mp4">
Caneventaketext</my-better-video>

```
内置指令是打包在AngularJS内部的指令。所有内置指令的命名空间都使用ng作为前缀。为了防止命名空间冲突，不要在自定义指令前加ng前缀。
   - 第一个自定义指令
   通过AngularJS模块API中的.directive()方法，我们可以通过传入一个字符串和一个函数来注册一个新指令。其中字符串是这个指令的名字，指令名应该是驼峰命名风格的，函数应该返回一个对象
   ```
   <my-directive></my-directive>
   angular.module('myApp',[])
   .directive('myDirective', function() {
   return {
   restrict: 'E',
   replace: true,//生成的代码中不含<my-directive></my-directive>
   template: '<a href="http://webywx.com">
   Click me to go to webywx</a>'
   };
   });
   ```
   注：在我们的例子中，在HTML里使用my-directive声明指令，因此指令定义必须以myDirective为名字（驼峰命名法）。
   
####声明指令本质上是在HTML中通过元素、属性、类或注释来添加功能。
   为了让AngularJS能够调用我们的指令，需要修改指令定义中的restrict设置。这个设置告诉AngularJS在编译HTML时用哪种声明格式来匹配指令定义。我们可以指定一个或多个格式。创建的指令中可以指定以元素（E）、属性（A）、类（C）或注释（M）的格式来调用指令，例：restrict: 'EAC'。
   无论有多少种方式可以声明指令，我们坚持使用属性方式，因为它有比较好的跨浏览器兼容性：
   ```
   <div my-directive></div>
   ```
   声明指令时既可以使用表达式，也可以不使用表达式。下面是几种合法的表达式声明：
   ```
   <my-directive="someExpression">
   </my-directive>
   <div my-directive="someExpression">
   </div>
   <div class="my-directive:someExpression">
   </div>
   <!-- directive: my-directive someExpression -->
   ```
   为了更加明确我们的意图，将restrict设置为字母A（代表attribute）：
   restrict: 'A
   遵循这个约定的同时，也要注意每个浏览器的内置样式，以便决定指令模板在HTML中是嵌套在声明元素内，还是替换声明元素。
   值得注意的一个例外是，扩展内置HTML标签，例如用AngularJS重载`<a>`、`<form>`和`<input>`。这些场景不会导致浏览器的兼容性问题，因为它们本身就是浏览器所支持的标签。好的经验法则就是始终用属性来声明指令，这样会给以后带来方便。
   注意，所有以ng前缀开头作为命名空间的指令都是AngularJS提供的内置指令，因此不要把你自己开发的指令以这个前缀命名。
   1. ng-disabled
   使用ng-disabled可以把disabled属性绑定到以下表单输入字段上：
   ```
    <input> （text、 checkbox、 radio、 number、 url, email、 submit）；
    <textarea>；
    <select>；
    <button>。
   ```
   当写普通的HTML输入字段时，如果在元素标签上出现了disabled属性就会禁用这个输入字段。通过ng-disabled可以对是否出现属性进行绑定。例如，在下面的例子中按钮会一直禁用， 直到用户在文本字段中输入内容：
   ```
   <input type="text" ng-model="someProperty" placeholder="TypetoEnable">
   <button ng-model="button" ng-disabled="!someProperty">AButton</button>
   ```
   2. ng-readonly
   同其他布尔属性一样， HTML定义只会检查readonly属性是否出现，而不是它的实际值。
   通过ng-readonly可以将某个返回真或假的表达式同是否出现readonly属性进行绑定：
   ```
   Type here to make sibling readonly:
   <input type="text" ng-model="someProperty"><br/>
   <input type="text"
   ng-readonly="someProperty"
   value="Some text here"/>
   ```
   3. ng-checked
   ```
   默认情况下会把复选框勾选：
   <label>someProperty = {{someProperty}}</label>
   <input type="checkbox"
   ng-checked="someProperty"
   ng-init="someProperty = true"
   ng-model="someProperty">
   ```
   4. ng-selected
   ng-selected可以对是否出现option标签的selected属性进行绑定：
   ```
   <label>Select Two Fish:</label>
   <input type="checkbox"
   ng-model="isTwoFish"><br/>
   <select>
   <option>One Fish</option>
   <option ng-selected="isTwoFish">Two Fish</option>
   </select>
   ```
   5. ng-href
   当使用当前作用域中的属性动态创建URL时，应该用ng-href代替href（用href会404）
   6. ng-src
   AngularJS会告诉浏览器在ng-src对应的表达式生效之前不要加载图像(用src会找不到图片)
   7. ng-app
   任何具有ng-app属性的DOM元素将被标记为$rootScope的起始点。
   $rootScope是作用域链的起始点，任何嵌套在ng-app内的指令都会继承它。
   在JavaScript代码中通过run方法来访问$rootScope。
   ```
   <html ng-app="myApp">
   <body>
   {{ someProperty }}
   <button ng-click="someAction()"></button>
   </body>
   </html>
   angular.module('myApp', [])
   .run(function($rootScope) {
   $rootScope.someProperty = 'hello computer';
   $rootScope.someAction = function() {
   $rootScope.someProperty = 'hello human';
   };
   });
   ```
   这里为了演示方便，像使用全局作用域一样使用$rootScope，实际生产中不建议这样做。
   8. ng-controller
   内置指令ng-controller的作用是为嵌套在其中的指令创建一个子作用域，避免将所有操作和模型都定义在$rootScope上。用这个指令可以在一个DOM元素上放置控制器。
   9. ng-include
   使用ng-include可以加载、编译并包含外部HTML片段到当前的应用中。模板的URL被限制
   在与应用文档相同的域和协议下，可以通过白名单或包装成被信任的值来突破限制。更进一步，
   需要考虑跨域资源共享（Cross-Origin Resource Sharing， CORS）和同源规则（Same Origin Policy）
   来确保模板可以在任何浏览器中正常加载。例如，所有浏览器都不能进行跨域的请求，部分浏览
   器也不能访问file://协议的资源。
   10. ng-switch
   这个指令和ng-switch-when及on="propertyName"一起使用，可以在propertyName发生变
   化时渲染不同指令到视图中。在下面的例子中，当person.name是Ari时，文本域下面的div会显
   示出来，并且这个人会获得胜利：
   ```
   <input type="text" ng-model="person.name"/>
   <div ng-switch on="person.name">
   <p ng-switch-default>And the winner is</p>
   <h1 ng-switch-when="Ari">{{ person.name }}</h1>
   </div>
   ```
   注意，在switch被调用之前我们用ng-switch-default来输出默认值。
   11. ng-view
   ng-view指令用来设置将被路由管理和放置在HTML中的视图的位置。
   12. ng-if
   使用ng-if指令可以完全根据表达式的值在DOM中生成或移除一个元素。如果赋值给ng-if的表达式的值是false，那对应的元素将会从DOM中移除，否则对应元素的一个克隆将被重新插入DOM中。
   ng-if同no-show和ng-hide指令最本质的区别是，它不是通过CSS显示或隐藏DOM节点，而是真正生成或移除节点.
   13. ng-repeat
   ng-repeat用来遍历一个集合或为集合中的每个元素生成一个模板实例。集合中的每个元素都会被赋予自己的模板和作用域。同时每个模板实例的作用域中都会暴露一些特殊的属性。
   ```
    $index：遍历的进度（0...length-1）。
    $first：当元素是遍历的第一个时值为true。
    $middle：当元素处于第一个和最后元素之间时值为true。
    $last：当元素是遍历的最后一个时值为true。
    $even：当$index值是偶数时值为true。
    $odd：当$index值是奇数时值为true。
   ```
  10. ng-bind
  尽管可以在视图中使用\{ \{ \} \}模板语法（AngularJS内置的方式），我们也可以通过ng-bind指令实现同样的行为。
  HTML加载含有\{ \{ \} \}语法的元素后并不会立刻渲染它们，导致未渲染内容闪烁（Flash of Unrendered Content， FOUC）。我可以用ng-bind将内容同元素绑定在一起避免FOUC。内容会被当作子文本节点渲染到含有ng-bind指令的元素内。
  11. ng-cloak
      除使用ng-bind来避免未渲染元素闪烁，还可以在含有\{ \{ \} \}的元素上使用ng-cloak指令：
      ```
      <body ng-init="greeting='HelloWorld'">
      <p ng-cloak>{{ greeting }}</p>
      </body>
      ```
      ng-cloak指令会将内部元素隐藏，直到路由调用对应的页面时才显示出来。
  12. ng-bind-template
  同ng-bind指令类似， ng-bind-template用来在视图中绑定多个表达式。
  ```
  <div
  ng-bind-template="{{message}}{{name}}">
  </div>
  ```
  13. ng-model
  ng-model指令用来将input、 select、 text area或自定义表单控件同包含它们的作用域中的属性进行绑定。它可以提供并处理表单验证功能，在元素上设置相关的CSS类（ng-valid、ng-invalid等），并负责在父表单中注册控件。
  它将当前作用域中运算表达式的值同给定的元素进行绑定。如果属性并不存在，它会隐式创建并将其添加到当前作用域中。我们应该始终用ngModel来绑定$scope上一个数据模型内的属性，而不是$scope上的属性，这可以避免在作用域或后代作用域中发生属性覆盖。
  ```
  例如：
  <input type="text"
  ng-model="modelName.someProperty" />
  ```
  14. ng-show/ng-hide
  ng-show和ng-hide根据所给表达式的值来显示或隐藏HTML元素。
  15. ng-change
  这个指令会在表单输入发生变化时计算给定表达式的值。因为要处理表单输入，这个指令要和ngModel联合起来使用。
  ```
  <div ng-controller="EquationController">
  <input type="text"
  ng-model="equation.x"
  ng-change="change()" />
  <code>{{ equation.output }}</code>
  </div>
  angular.module('myApp',[])
  .controller('EquationController',function($scope) {
  $scope.equation = {};
  $scope.change = function() {
  $scope.equation.output
  = parseInt($scope.equation.x) + 2;
  };
  });
  ```
  上面的例子中，只要文本输入字段中的内容发生了变化就会改变equation.x的值，进而运行change()函数。
  16. ng-form
  ng-form用来在一个表单内部嵌套另一个表单。普通的HTML <form>标签不允许嵌套，但ng-form可以。
  这意味着内部所有的子表单都合法时，外部的表单才会合法。这对于用ng-repeat动态创建表单是非常有用的。
  由于不能通过字符插值来给输入元素动态地生成name属性，所以需要将ng-form指令内每组重复的输入字段都包含在一个外部表单元素内。
  下面的CSS类会根据表单的验证状态自动设置：
   1.  表单合法时设置ng-valid；
   2.  表单不合法时设置ng-invlid；
   3.  表单未进行修改时设置ng-pristion；
   4.  表单进行过修改时设置ng-dirty。
  Angular不会将表单提交到服务器，除非它指定了action属性。要指定提交表单时调用哪个JavaScript方法，使用下面两个指令中的一个。
   1.  ng-submit：在表单元素上使用。
   2.  ng-click：在第一个按钮或submit类型（input[type=submit]）的输入字段上使用。
  为了避免处理程序被多次调用，只使用下面两个指令中的一个。
  下面的例子展示了如何通过服务器返回的JSON数据动态生成一个表单。我们用ng-loop来遍历从服务器取回的所有数据。由于不能动态生成name属性，而我们又需要这个属性做验证，所以在循环的过程中会为每一个字段都生成一个新表单。由于AngularJS中用来取代<form>的ng-form指令可以嵌套，并且外部表单在所有子表单都合法之前一直处于不合法状态，因此我们可以在动态生成子表单的同时使用表单验证功能。是的，鱼和熊掌可以兼得。
  下面先看一下我们硬编码的JSON数据，把它假设成是从服务器返回的：
  ```
  angular.module('myApp',[])
  .controller('FormController',function($scope) {
  $scope.fields = [
  {placeholder: 'Username', isRequired: true},
  {placeholder: 'Password', isRequired: true},
  {placeholder: 'Email (optional)', isRequired: false}
  ];
  $scope.submitForm = function() {
  alert("it works!");
  };
  });
  下面用这些数据生成一个有验证功能的动态表单：
  <form name="signup_form"
  ng-controller="FormController"
  ng-submit="submitForm()" novalidate>
  <div ng-repeat="field in fields"
  ng-form="signup_form_input">
  <input type="text"
  name="dynamic_input"
  ng-required="field.isRequired"
  ng-model="field.name"
  placeholder="{{field.placeholder}}" />
  <div
  ng-show="signup_form_input.dynamic_input.$dirty &&
  signup_form_input.dynamic_input.$invalid">
  <span class="error"
  ng-show="signup_form_input.dynamic_input.$error.required">
  The field is required.
  </span>
  </div>
  </div>
  <button type="submit"
  ng-disabled="signup_form.$invalid">
  Submit All
  </button>
  </form>
  input.ng-invalid {
  border: 1px solid red;
  }
  input.ng-valid {
  border: 1px solid green;
  }
  ```
  17. ng-click
  ng-click用来指定一个元素被点击时调用的方法或表达式。
  18. ng-select
  ng-select用来将数据同HTML的`<select>`元素进行绑定。这个指令可以和ng-model以及ng-options指令一同使用，构建精细且表现优良的动态表单。
  ng-options的值可以是一个内涵表达式（comprehension expression），其实这只是一种有趣的说法，简单来说就是它可以接受一个数组或对象，并对它们进行循环，将内部的内容提供给select标签内部的选项。它可以是下面两种形式。
   1.  数组作为数据源：
   -  用数组中的值做标签；
   -  用数组中的值作为选中的标签；
   -  用数组中的值做标签组；
   -  用数组中的值作为选中的标签组。
   2.  对象作为数据源：
   -  用对象的键-值（key-value）做标签；
   -  用对象的键-值作为选中的标签；
   -  用对象的键-值作为标签组；
   -  用对象的键-值作为选中的标签组。
  下面看一个ng-select指令的实例：
  ```
  <div ng-controller="CityController">
  <select ng-model="city"
  ng-options="city.name for city in cities">
  <option value="">Choose City</option>
  </select>
  Best City: {{ city.name }}
  </div>
  angular.module('myApp',[])
  .controller('CityController',function($scope) {
  $scope.cities = [
  {name: 'Seattle'},
  {name: 'San Francisco'},
  {name: 'Chicago'},
  {name: 'New York'},
  {name: 'Boston'}
  ];
  });
  ```

  19. ng-submit
  ng-submit用来将表达式同onsubmit事件进行绑定。这个指令同时会阻止默认行为（发送请求并重新加载页面）， 除非表单不含有action属性。
  ```
  <form ng-submit="submit()"
  ng-controller="FormController">
  Enter text and hit enter:
  <input type="text"
  ng-model="person.name"
  name="person.name" />
  <input type="submit"
  name="person.name"
  value="Submit" />
  <code>people={{people}}</code>
  <ul ng-repeat="(index, object) in people">
  <li>{{ object.name }}</li>
  </ul>
  </form>
  angular.module('myApp',[])
  .controller('FormController',function($scope) {
  $scope.person = {
  name: null
  };
  $scope.people = [];
  $scope.submit = function() {
  if ($scope.person.name) {
  $scope.people.push({name: $scope.person.name});
  $scope.person.name = '';
  }
  };
  });
  ```
  20. ng-class
  使用ng-class 动态设置元素的类，方法是绑定一个代表所有需要添加的类的表达式。重复的类不会添加。当表达式发生变化，先前添加的类会被移除，新类会被添加。
  下面的例子会用ng-class在一个随机数大于5时将.red类添加到一个div上：
  ```
  <div ng-controller="LotteryController">
  <div ng-class="{red: x > 5}"//ng-class="{red: x > 5,green:x<5} 加多个类时用逗号隔开即可。
  ng-if="x > 5">
  You won!
  </div>
  <button ng-click="generateNumber()"
  ng-init="x = 0">
  Draw Number
  </button>
  <p>Number is: {{ x }}</p>
  </div>
  
  .red {
  background-color: red;
  }
  angular.module('myApp',[])
  .controller('LotteryController', function($scope) {
  $scope.generateNumber = function() {
  return Math.floor((Math.random()*10)+1);
  };
  })
  ```
  21. ng-attr-(suffix)
  当AngularJS编译DOM时会查找花括号\{ \{ some expression \} \}内的表达式。这些表达式会被自动注册到$watch服务中并更新到$digest循环中，成为它的一部分：
  ```
  <-- updated when`someExpression` on the$scope
  is updated -->
  <h1>Hello{{someExpression}}</h1>
  有时浏览器会对属性会进行很苛刻的限制。 SVG就是一个例子：
  <svg>
  <circle cx="{{ cx }}"></circle>
  </svg>
  运行上面的代码会抛出一个错误，指出我们有一个非法属性。可以用ng-attr-cx来解决这
  个问题。注意， cx位于这个名称的尾部。在这个属性中，通过用{{ }}来写表达式，达到前面提
  到的目的。
  <svg>
  <circle ng-attr-cx="{{ cx }}"><circle>
  </svg>
  {{person.name || "无"}} //绑定时做简单判断可以这么写。
  ```
  
  
