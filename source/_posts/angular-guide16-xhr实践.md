---
title: Angular中的异步请求-xhr
date: 2016-07-20 02:30:12
tags: angular、$http
---
### 跨域和同源策略
  　同源策略允许页面从同一个站点加载和执行特定的脚本。浏览器通过对比每一个资源的协议、主机名和端口号来判断资源是否与页面同源。站外其他来源的脚本同页面的交互则被严格限制。跨域资源共享（Cross Origin Resource Sharing， CORS）是一个解决跨域问题的好方法，从而可以使用XHR从不同的源加载数据和资源．除此之外：还能使用jsonp,服务器代理（它需要服务器端的额外支持）。
   1. jsonp
   JSONP的原理是通过`<script>`标签发起一个GET请求来取代XHR请求。 JSONP生成一个`<script>`标签并插到DOM中，然后浏览器会接管并向src属性所指向的地址发送请求。
   当服务器返回请求时，响应结果会被包装成一个JavaScript函数，并由该请求所对应的回调函数调用。
   AngularJS在$http服务中提供了一个JSONP辅助函数。通过$http服务的jsonp方法可以发送请求，如下所示：
   ```
   $http
   .jsonp("https://api.github.com?callback=JSON_CALLBACK") .success(function(data) {
   // 数据
   });
   ```
   当请求被发送时， AngularJS会在DOM中生成一个如下所示的`<script>`标签：
   ```
   <script src="https://api.github.com?callback=angular.callbacks._0"
   type="text/javascript"></script>
   ```
   注意， JSON_CALLBACK被替换成了一个特地为此请求生成的自定义函数。当支持 JSOPN的服务器返回数据时，数据会被包装在由 AngularJS生成的具名函数angular.callbacks._0中。
   在这个例子中， GitHub服务器会返回包含在回调函数中的JSON数据，响应看起来如下所示：
   ```
   // 简写
   angular.callbacks._0({
   'meta': {
   'X-RateLimit-Limit': '60',
   'status': 200
   },
   'data': {
   'current_user_url': 'https://api.github.com/user'
   }
   })
   ```
   当AngularJS调用指定的回调函数时会对$http的promise对象进行resolve。
    jsonp的缺点：1、只能发送get请求。2.有潜在的安全风险。
   2. 使用 CORS
   CORS规范简单地扩展了标准的XHR对象，以允许JavaScript发送跨域的XHR请求。它会通过预检查（preflight）来确认是否有权限向目标服务器发送请求。
   预检查可以让服务器接受或拒绝来自全部服务器、特定服务器或一组服务器的请求。这意味着客户端和服务端应用需要协同工作，才能向客户端或服务器发送数据。
   1. 设置
    为了在AngularJS中使用CORS，首先需要告诉AngularJS我们正在使用CORS。使用config()方法在应用模块上设置两个参数以达到此目的。
    首先，告诉AngularJS使用XDomain，并从所有的请求中把X-Request-With头移除掉。
    X-Request-With头默认就是移除掉的，但是再次确认它已经被移除没有坏处。
    ```
    angular.module('myApp', [])
    .config(function($httpProvider) {
    $httpProvider.defaults.useXDomain = true;
    delete $httpProvider.defaults.headers
    .common['X-Requested-With'];
    });
    ```
    现在可以发送CORS请求了.
   2. 服务器端CORS支持
   支持CORS的服务器必须在响应中加入几个访问控制相关的头。
    -  Access-Control-Allow-Origin
   这个头的值可以是与请求头的值相呼应的值，也可以是*，从而允许接收从任何来源发来的请求。
    -  Access-Control-Allow-Credentials（可选）
   默认情况下， CORS请求不会发送cookie。如果服务器返回了这个头，那么就可以通过将withCredentials设置为true来将cookie同请求一同发送出去。
       如果将$http发送的请求中的withCredentials设置为true，但服务器没有返回AccessControl-Allow-Credentials，请求就会失败，反之亦然。
       后端服务器必须能处理OPTIONS方法的HTTP请求。
       CORS请求分为简单和非简单两种类型。
        1. 简单请求
        如果请求使用下面一种HTTP方法就是简单请求：HEAD；GET；POST。
        如果请求除了下面列表中的一个或多个HTTP头以外，没有使用其他头： Accept； Accept-Language ； Content-Language； Last-Event-ID； Content-Type；application/x-www-form-urlencoded；multipart/form-data；text/plain。
        我们把这类请求归类为简单请求，因为浏览器可以不需要使用CORS就发送这类请求。简单请求不要求浏览器和服务器之间有任何的特殊通信。
        一个使用$http服务的简单CORS请求和其他简单请求看起来是下面这样的：
        ```
        $http
        .get("https://api.github.com")
        .success(function(data) {
        // 数据
        });
        ```
        2. 非简单请求
        不符合简单请求标准的请求被称为非简单请求。如果想要支持PUT或DELETE方法，又或者想给请求设置特殊的内容类型，就需要发送非简单请求。 尽管这些请求在客户端开发者看来没什么不同，但浏览器会以不同的方式处理它们。
        浏览器实际上会发送两个请求：预请求和请求。浏览器首先会向服务器发送预请求来获得发送请求的许可，只有许可通过了，浏览器才会发送真正的请求。
        浏览器处理CORS的过程是透明的。
        同简单请求一样，浏览器会给预请求和请求都加上Origin头。
        - 预请求
        浏览器发送的预请求是OPTIONS类型的，预请求中包含以下头信息：
        1.  Access-Control-Request-Method
        这个头是请求所使用的HTTP方法，会始终包含在请求中。
        2.  Access-Control-Request-Headers （可选)
        这个头的值是一个以逗号分隔的非简单头列表，列表中的每个头都会包含在这个请求中。
        服务器必须接受这个请求，然后检查HTTP方法和头的合法性。如果通过了检查，服务器会
        在响应中添加下面这个头：
        3.  Access-Control-Allow-Origin
        这个头的值必须和请求的来源相同，或者是*符号，以允许接受来自任何来源的请求。
        4.  Access-Control-Allow-Methods
        这是一个可以接受的HTTP方法列表，对在客户端缓存响应结果很有帮助，并且未来发送的
        请求可以不必总是发送预请求。
        5.  Access-Control-Allow-Headers
        如果设置了Access-Control-Request-Headers头，服务器必须在响应中添加同一个头。
        我们希望服务器在可以接受这个请求时返回200状态码。如果服务器返回了200状态码，真正
        的请求才会发出。
        CORS并不是一个安全机制，只是现代浏览器实现的一个标准。在应用中设置安
        全策略依然是我们的责任。
        AngularJS中的非简单请求与普通请求看起来没有什么区别：
        ```
        $http
        .delete("https://api.github.com/api/users/1")
        .success(function(data) {
        // 数据
        });
        ```
   3. 服务器端代理
    实现向所有服务器发送请求的最简单方式是使用服务器端代理。这个服务器和页面处在同一 个域中（或者不在同一个域中但支持CORS），做为所有远程资源的代理。
    可以简单地通过使用本地服务器来代替客户端向外部资源发送请求，并将响应结果返回给客
    户端。通过这种方式，老式浏览器不必使用需要发送额外请求的CORS（只有现代浏览器支持
    CORS）也能发送跨域请求，并且可以在浏览器中采用标准的安全策略。为了实现服务器端代理，需要架设一个本地服务器来处理我们所有的请求，并负责向第三方发送实际的请求.
#### 数据格式：常见的有json、xml。
   若服务器返回的是json格式的，angular能正常解析，假如服务器返回的是XML而非JSON格式的数据，需要将其转换成JavaScript对象。有不少出色的开源库可以使用，同样，某些浏览器也内置了解析器，可以帮助我们将XML格式转换成JavaScript对象
   #### 下面介绍一个非常好用的开源库——X2JS
   首选需要确保安装了X2JS库，我们通过Bower来安装：
   ```
   $ bower install x2js
   ```
   然后可以在页面中从googlecode.com或我们自己安装的Bower组件中引用这个库：
   ```
   <script type="text/javascript"
   src="https://x2js.googlecode.com/hg/xml2json.js"></script>
   <!-- 或者 -->
   <script type="text/javascript"
   src="bower_components/xml2json/xml2json.js"></script>
   ```
   创建一个工厂服务以开始使用这个轻量的XML解析器，这个服务的功能很简单，就是在DOM中解析XML：
   ```
   angular.factory('xmlParser', function() {
   var x2js = new X2JS();
   return {
   xml2json: x2js.xml2json,
   json2xml: x2js.json2xml_str
   };
   });
   ```
   借助这个轻量的解析服务，可以将$http请求返回的XML解析成JSON格式，如下所示：
   ```
   angular.factory('Data', [$http, 'xmlParser', function($http, xmlParser) {
   $http.get('/api/msgs.xml', {
   transformResponse: function(data) {
   return xmlParser.xml2json(data);
   }
   });
   });
   ```
   现在请求的结果被转换成了JSON对象，可以像服务器本来返回的就是JSON格式一样来使用这个对象。
#### 使用 AngularJS 进行身份验证
   首先必须保证服务器端API的安全性。由于我们处理的代码是未编译的，且可能是由不信任的源发送的，不能假设所有的用户都聪明到可以认识到这些潜在的风险。
   下面介绍常被用来保护客户端应用的两种方法。
   1. 服务器端视图渲染
   如果站点所有的HTML页面都是由后端服务器处理的，可以使用传统的授权方式，由服务器端进行鉴权，只发送客户端需要的HTML。
   2. 纯客户端身份验证
   我们希望客户端和服务端的开发工作可以解耦并各自独立进行，且可以将组件独立地发布到生产环境中，互相没有影响。因此，需要通过使用服务器端API来保护客户端身份验证的安全，但并不依赖这些API来进行身份验证。
   通过令牌授权来实现客户端身份验证，服务器需要做的是给客户端应用提供授权令牌。
   令牌本身是一个由服务器端生成的随机字符串，由数字和字母组成，它与特定的用户会话相关联。
   **注：uuid库是用来生成令牌的好选择**
  当用户登录到我们的站点后，服务器会生成一个随机的令牌，并将用户会话同令牌之间建立关联，用户无需将ID或其他身份验证信息发送给服务器。
  客户端发送的每个请求都应该包含此令牌，这样服务器才能根据令牌来对请求的发送者进行身份验证。
  服务器端则无论请求是否合法，都会将对应事件的状态码返回给客户端，这样客户端才能做出响应。
##### 当客户端收到这些状态码时会做出相应的响应。数据流程如下：
   1.  一个未经过身份验证的用户浏览了我们的站点；
   2.  用户试图访问一个受保护的资源，被重定向到登录页面，或者用户手动访问了登录页面；
   3.  用户输入了他的登录ID（用户名或电子邮箱）以及密码，接着AngularJS应用通过POST用户的信息发送给服务端；
   4.  服务端对ID和密码进行校验，检查它们是否匹配；
   5.  如果ID和密码匹配，服务端生成一个唯一的令牌，并将其同一个状态码为200的响应一起返回。如果ID和密码不匹配，服务器返回一个状态码为401的响应。注：401状态码：未授权的请求。

##### 对一个已经通过身份验证的用户（通过了上面5个步骤的用户），流程如下：
   1. 用户请求一个受保护的资源路径（比如他自己的账号页面）；
   2. 如果用户尚未登录，应用会将他重定向到登录页面。如果用户登录了，应用会使用该会话对应的令牌来发送请求；
   3. 服务器对令牌进行校验，并根据请求返回合适的数据。
