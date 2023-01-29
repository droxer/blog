---
title: 理解 Reactive Functional Programming
date: 2015-12-26
scheduled: 2015-12-26
tags: programming
layout: layouts/post.njk
---

自己在学习了解reactive functional programming的过程中，看了很多文章，视频，阅读了一些现有实现的源代码，甚至是自己去尝试实现一个简单的[reactive框架][1]。而在这些之后才对reactive funcational programming有一个一点点肤浅的理解。

学习过程中，按照自己以往的习惯，我先去[wikipedia][2]，想要对这个东西有一个初步的理解，明白它是什么东西，解决什么问题。而[wikipedia][2]上面的第一句话是

>"Functional reactive programming (FRP) is a programming paradigm for reactive programming (asynchronous dataflow programming) using the building blocks of functional programming (e.g. map, reduce, filter)."

当时自己看到这个时，觉得这个描述过于抽象。自己一下子根本无法理解，但实际上当自己看完大量的文章视频之后，发现这个真的才是最准确的定义。

## asynchronous dataflow programming

dataflow可以是web应用中页面上用户的点击，系统中的一个个事件，包括用户的一个输入，对一个list的迭代等等。asynchronous dataflow programming是说，我们会异步的方式捕捉这些数据流，并在某些特定值或者满足某些特定条件的情况下执行相应的操作。这非常类似我们在设计模式中[观察者模式][3]。

<pre>
    <code>
c 代表鼠标的单击
---> 代表时间线

--c----c-c----c---c---->
    </code>
</pre>

## reactive functional programming

根据[wiki][1]的定义，是我们使用一些函数式编程中的方法对来操作这些asynchronous dataflow。：

<pre>
    <code>
鼠标的点击事件流:
               ---c----cc--c----ccc----c-->

               bufer(clickStream.throttle(250ms))

               --1c----2c---1c---3c---1c--->

               map(e.length)

               ---1-----2----1----3----1--->

               filter(x >= 2)

               ---------2---------2--------->
    </code>
</pre>

在上图，我们看到一个鼠标点击的asynchronous dataflow。首先，我们计算出每250ms的鼠标点击次数，然后使用map函数影射出每250ms内鼠标的点击次数，最后filter出两次以上的点击。最后我们可以根据我们最后的结果来执行相应的操作，比如说我们认为只有这样过滤出来的鼠标点击，我们认为是一次用户的双击操作。

上面是我对于Reactive Functional Programming一个非常粗浅的理解。如果大家想了解细节更多可以看看这个视频[Functional Reactive Programming in the Netflix API](http://www.infoq.com/presentations/Netflix-API-rxjava-hystrix)，另外[ReactiveX](https://github.com/ReactiveX)里有很多不同语言上对于Reactive Functional Programming的实现。


[1]: https://github.com/droxer/RxGo/    "RxGo"
[2]: https://en.wikipedia.org/wiki/Functional_reactive_programming/  "wikipidea"
[3]: https://en.wikipedia.org/wiki/Observer_pattern/    "observer pattern"
[4]: https://github.com/ReactiveX/RxJava    "RxJava"
