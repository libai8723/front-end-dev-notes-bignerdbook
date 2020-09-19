---
title: "Some notes to Spring in Action 4th edition 4th chapter 3.4 Section- Annotating introduction" 
date: 2020-09-19
tag: "Spring in Action 4th edition"
---

## Annotating introductions

Some languages, such as Ruby and Groovy, have the notion of open classes. They make it possible to add new methods to an object or class without directly changing the definition of those objects or classes. Unfortunately, Java isn’t that dynamic. Once a class has been compiled, there’s little you can do to append new functionality to it.

对于有些编程语言来说，例如Ruby和Groovy这种，有一种叫做开放性类（open class）的说法。这种东西的存在让我们可以在不改变原有的对象和类型的情况下增加一些新的方法。但是很不幸的是，java没有那么的动态，一旦一个类被编译出来了，那么我们能做的事情就很少了。

But if you think about it, isn’t that what you’ve been doing in this chapter with aspects? Sure, you haven’t added any new methods to objects, but you’re adding new functionality around the methods that the objects already have. If an aspect can wrap existing methods with additional functionality, why not add new methods to the object? In fact, using an AOP concept known as introduction, aspects can attach new methods to Spring beans.

但是如果你仔细思考一下这个事情，你会发现在最近的这一章中我们使用aspect不就是在干这个事情吗。当然我们做的事情并没有给已经存在的类添加新的方法，我们仅仅是在已经存在的方法周围增加一些功能。如果一个切面可以包装现有的方法来实现一些额外的功能的话，那为什么不能添加一些新的方法到对象上。而实际上来看，这个给已有的对象和类增加功能的概念，在AOP中被叫做“引入”，aspects可以附加新的方法到Spring的Beans

Recall that in Spring, aspects are proxies that implement the same interfaces as the beans they wrap. What if, in addition to implementing those interfaces, the proxy is also exposed through some new interface? Then any bean that’s advised by the aspect will appear to implement the new interface, even if its underlying implementation class doesn’t. Figure 4.7 illustrates how this works.

让我们回忆一下，在Spring中，切面实际上就是一些代理，这些代理实现了和它们包裹的beans所具备的相同的接口。那么让我们来假设一下，除了实现这些接口之外，这个代理同时还通过一些新的接口来暴露出来一些能力。这样的话，任何被aspect所包裹的bean，都看起来好像是实现了那个新的接口，尽管在这个接口之下的实现的类并没有这个功能。下图中的4.7作了一个示意图来方便理解

![Annotation Introduction](/front-end-dev-notes-bignerdbook/assets/img/SpringInActionChapter4-3AnnotationIntroduction.png)

Notice that when a method on the introduced interface is called, the proxy dele-
gates the call to some other object that provides the implementation of the new
interface. Effectively, this gives you one bean whose implementation is split across
multiple classes.

注意，当被引入的interface上的方法被调用的时候，这个代理会把对应的方法调用的请求分发到其他对应的对象上，这个对象提供了这个新的接口的对应的方法的实现；通过这种方式，给我们了一种把一个bean的实现，分割到若干个classes上的能力