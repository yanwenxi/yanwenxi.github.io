---
title: 了解下框架模式 -MVP
date: 2016-12-16 09:43:15
tags: mvp
---
#### M-V-P:(模型，视图，派发器)
　　mvc的弊端：View 和 Model 并不是完全脱离的，还是有一些逻辑耦合，因为需要根据修改后的model从新刷新view。 难免view里面沾染一点model的结构。不方便进行更精细的颗粒化控制。(因为view只知道 model被改了，但不知道谁改的!)，model在对应多个view的时候，很难都伺候到位。
<div align=center>
    ![](http://oi28tswuq.bkt.clouddn.com/images/mvp.png-public)
</div>

　　针对mvc的一些问题，在mvp模式下， 斩断了 view 与 model的关系， 当m 改变时，m 通知 p 去改变v， 所以v变得更纯洁(刷新逻辑被移动到了p层)， 为了保证m可以最大程度的复用 一部分业务逻辑也从 m 转移到了p所以 mvp下 p 非常厚实。
  
  　mvp中最后改变v的是p那么在 v与p 之间会有一个接口，解决怎么转换以及传值的问题。