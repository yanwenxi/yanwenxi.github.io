---
title: chrome 控制台调试angularjs代码逻辑
date: 2016-01-25 14:02:46
tags: debugger
---
##### chrome浏览器开发者工具中sources中的VM+数字是怎么一回事
Chrome控制台中的VM，一般是自己在控制台写的JS代码片段（或者网页Ajax获取的代码片段）运行后的提示。
这是为了表示代码来源，与JS文件中的代码进行区分。
VM是 Virtual Machine的缩写，其实代指Chrome的V8引擎。
后面加数字，是编号，不同的代码片段，Chrome会自动按顺序分配一个id编号。

#### 善用控制台搜索功能

<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/console.png-public)
</div>
调试时，想要找源文件中的某个函数或者变量，都可以用在源文件中按ctrl+f搜索。
想在控制台中查看自己定义的变量时，也可以用search功能。debugger://VM3189就是指的上面说的VM+数字;

#### angular中怎么在控制台调试controller中的数据以及修改后台接口返回来的数据。
1.controller中加上var vm= this;（指明控制台的vm指的是页面中的this,此处的vm可以换成其他变量，此处的vm和上面说的VM没有半毛线关系，只是controller里习惯了这么写而已。） 在对应的调试的位置打上断点debugger;
2.然后在chrorme 的控制台中输入window.aa=vm.xxx;假设vm.xxx为ajax请求回来的数据。
3.此时在控制台中输入aa按回车即可看到ajax请求回来的数据，此时用aa变量就可以查看和更改ajax请求回来的数据了。
4.利用更改aa的值很容易就能调试页面了，想让此时的页面渲染什么样的数据都可以，这样的好处是页面能渲染aa的值，当页面刷新时，他还是渲染ajax请求回来的数据。实现了更改后端返回来的数据的目的了，极大地方便了自己调试。想让页面渲染啥值就渲染啥值。
5.利用的原理：对象是引用数据类型的，存储的时候是以地址存储的，不是以值存储。例：window.aa=vm.xxx，本来页面渲染的是vm.xxx这个对象的数据，此时你让window.aa等于vm.xxx这个对象，你更改了aa的值就相当于更改了vm.xxx的值，就这么简单的原理。

#### 断点调试问题
<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/debug.png)
</div>
注意调试时，可以打多个断点方便调试，在源码中打一个断点即可，当控制台停到debugger处，可以在控制台的源文件中行号前根据逻辑打断点，该一步步走时就一步步地调， 可以查看函数的执行过程，也可以略过。调试异步时，可以在成功的回调函数处打断点，直接就能查看请求回来的数据了。不要一步步地走，得点好多好多次。注意代码的执行顺序。配合着在控制台想看某些变量的值，看看是否与逻辑相符即可。

#### 通过ng-click传参来实现同一个controller中获取不同的数据列表，进而页面显示不同的内容
ng-click的时候可以进行传参，如：ng-click="controller.aaa=1;controller.getList()" 此时执行点击操作时控制器中能收到aaa=1这个参数并且会执行getList这个函数。可以根据传过去aaa值的不同来执行不同的接口获取列表，写一个getList方法，在getList方法里根据传过来的值不同做判断，不同的值走不同接口，此时达到了都执行getList这个函数，但是会请求不同的端口。

#### ng-model="controller.param.time" 注意绑定的是date对象类型才会显示时间
var vm.param.time=new Date();此时页面会显示时间，因为time是日期对象类型的。
<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/date.png-public)
</div>
项目中，利用日期查询订单时，一般把date类型格式化成字符串形式比如：“2016-01-01”，如果此时vm.param.time直接等于这个字符串“2016-01-01”，此时页面将不能正常显示时间。
解决办法：var obj={time:''};obj.time= moment(vm.param.time).format('YYYY-MM-DD');请求查询订单时把格式化后的日期obj当请求参数传进去即可，注：要一直保证vm.param.time是一个date类型。否则页面就不能正常显示。

#### 调试总比苦思冥想要好
当页面展现的内容不是自己想要的时候，在控制台network里调试，看看该操作走的是哪个接口，是不是自己想让它走的那个接口。还可以看看该操作都走了哪几个接口，与逻辑是否相符。

自己想要的结果没出来用debugger调试，控制台看看值传过去了没有，如果没有就传，把自己的想法通过debugger的形式来调试然后把想法通过代码加进去。遇到问题静心想逻辑，逻辑，逻辑。有问题了一个一个解决，不要同时想好几个问题，把一个问题解决了再说另一个问题，一块想好几个问题容易乱套。问题都是一个一个解决的。想一个问题时要想为什么没有达到自己的预期，是哪里的问题，如何通过debugger来解决。

#### 总结
遇到与自己想的结果不一样时，莫捉急，要多打debugger调试，不要空想，调试真的能解决很多问题。