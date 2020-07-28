---
title: "Some notes to Spring in Action 4th edition 4th chapter - AOP" 
date: 2020-01-31
tag: Spring, Aspect Oriented Programming
---

## What is Cross-Cutting Concern

In software development, functions that span multiple points of an application are called **cross-cutting concerns**. Typically, these cross-cutting concerns are conceptually separate from (but often embedded directly within) the application’s business logic. Separating these cross-cutting concerns from the business logic is where aspect oriented programming (AOP) goes to work.

在软件开发中，有一些函数功能会横跨一个应用中的多个点，这种功能被叫做cross-cutting concerns. 通常来说这些cross-cutting concerns在概念上是和应用的业务逻辑相分离的，但是表现在代码中的位置经常是嵌入到业务逻辑代码之中的。把这些cross-cutting concerns的功能和业务逻辑相分离，就是AOP要做的事情。

关于什么东西可能成为cross-cutting concerns，在wiki上写的挺好的：[Example of Cross-Cutting Concern](https://en.wikipedia.org/wiki/Cross-cutting_concern#Examples)

在wiki中提到了ECOOP 1997的一篇文章，看了一下，没看懂，不看了。

本书有一句话还是说的比较好看的：In chapter 2, you learned how to use dependency injection to manage and configure application objects. Whereas DI helps you decouple application objects from each other, AOP helps you decouple cross-cutting concerns from the objects they affect.

DI用来管理和配置构成应用程序的objects，让这些object彼此之间解耦。而AOP帮助我们把cross-cutting concerns和它们影响的对象解耦。

Logging是一个常见的关于aspect的例子，其他的还有事务，安全，缓存，等等。

### What is aspect-oriented programming

As stated earlier, aspects help to modularize cross-cutting concerns. In short, a crosscutting concern can be described as any functionality that affects multiple points of an application. Security, for example, is a cross-cutting concern, in that many methods in an application can have security rules applied to them.

像之前描述的，aspects（切面）可以用来帮助我们把cross-cutting concerns模块化。简而言之，任何影响到应用的多个component的功能点，都可以被描述为一个cross-cutting concern。比如，拿安全举例，就是一个cross-cutting concern，因为在一个应用中的很多方法都有安全规则。

但是我感觉还不够贴切，还是没搞懂，什么时候用OOP，什么时候用AOP。但是作者在第四章一开始的时候，给了一个显示生活中的例子。

他说的是电表的例子，电表计量电力的使用，对于一家之主来说，更多是被动的，无感知的。但是没有计量的话，又确实不行。当然也不能把计量电力的责任给到一家之主，当作他的主要的责任（所以这个东西不是他的领域的主要工作，Domain），一家之主的工作比如有，剪草坪，打扫浴室等等。

有一些软件系统的功能就像这个电表一样，在到处都有，但是我们又不想显式的去调用meters.metric()的函数来进行电费的计量。这种就比较适合AOP了吧。

作者在Page99的地方有一句话非常的精髓，但是看不懂，需要找更多的关于OOP本身的书籍才能看懂，他的原话是这么说的：

A common object-oriented technique for reusing common functionality is to apply inheritance or delegation. But inheritance can lead to a brittle object hierarchy if the same base class is used throughout an application, and delegation can be cumbersome because complicated calls to the delegate object may be required.

在OO中想要重用一些common的功能点的话，主要使用的技术是inheritance和delegation，也就是继承和代理。但是如果同一个基础类在一整体应用程序中被继承使用的话，很容易导致脆弱的对象层次结构，简单理解的话就是，修改了基础类的一个点，其他的所有的继承类，都要修改；同样使用委托的话，也会很麻烦，因为可能因为不同的情况需要代理给不同的对象来实际执行动作。

@todo

读到这里的时候，我就好奇了，那么对于OOP来说，OOP有哪些Concept和Technique呢？看到这里的时候，我才意识到，实际上我并没有深入的阅读过一些专门关于OOP的书籍，所以我搜索了一下针对OOP有哪些书籍，找了一些参考的书籍：[OOP wiki's further reading](https://en.wikipedia.org/wiki/Object-oriented_programming#Further_reading)

接着读书上的说明，
Aspects offer an alternative to inheritance and delegation that can be cleaner in many circumstances. With AOP, you still define the common functionality in one place, but you can declaratively define how and where this functionality is applied without having to modify the class to which you’re applying the new feature. Crosscutting concerns can now be modularized into special classes called aspects. This has two benefits. First, the logic for each concern is in one place, as opposed to being scattered all over the code base. Second, your service modules are cleaner because they only contain code for their primary concern (or core functionality), and secondary concerns have been moved to aspects.

Aspects在继承和代理之外提供了另外一种可选的方式，在很多情况下这个方式都很好用。你可以在一个地方定义一些公用的功能点，然后你可以通过声明的方式来决定这些功能点在哪一个类上被使用，而不用修改这个被使用的类。这样的话CrossCutting Concerns就可以被模块化到一些特殊的类中，被称为aspects。好处显而易见，就像上面说的2点。

OK，读到这里的时候其实已经非常明白，Aspects具体是干什么用途的了。所以下面开始，我们开始看一下AOP的一些基本的术语 Terminology

### AOP Terminology

Like most technologies, AOP has its own jargon. Aspects are often described in terms of advice, pointcuts, and join points. Unfortunately, many of the terms used to describe AOP features aren’t intuitive. Nevertheless, they’re now part of the AOP idiom, and in order to understand AOP, you must know these terms. Before you walk the walk, you have to learn to talk the talk.

就像大多数其他的技术一样，AOP也有自己的特定的术语。Aspects经常使用advice，pointcuts和join points来描述。不幸的是，这些术语并不直观，然而正是这些术语构成了AOP的成语，所以为了理解AOP，我们必须熟悉这些术语，在我们学会走之前，我们必须学会talk with这些术语

#### Advice

When a meter reader shows up at your house, his purpose is to report the number of kilowatt hours back to the electric company. Sure, he has a list of houses that he must visit, and the information he reports is important. But the actual act of recording electricity usage is the meter reader’s main job.

作者还是通过打比方的方式来说明什么是Advice，当一个抄表员出现在你的房子面前的时候，他的主要的意图是来抄表，当然他还有维护一个清单，清单上写着他要去访问多少间其他的房子，他还需要形成他的报告，这一切都很重要。但是记录电表的读数的使用才是抄表员的主要的工作。

Likewise, aspects have a purpose—a job they’re meant to do. In AOP terms, the job of an aspect is called advice. Advice defines both the what and the when of an aspect. In addition to describing the job that an aspect will perform, advice addresses the question of when to perform the job.

同样的，aspects也有一个意图，就是它们主要相干的工作是什么。在AOP的术语中，aspect的job我们把它称为一个advice。advice同时定义了一个aspect需要做什么，和在什么时候做。也就是说，除了描述了Aspect需要做的工作之外，advice还定义了什么时候来执行这个工作。

Should it be applied before a method is invoked? After the method is invoked? Both before and after method invocation? Or should it be applied only if a method throws an exception? Spring aspects can work with five kinds of advice:

1. Before—The advice functionality takes place before the advised method is
invoked.
2. After—The advice functionality takes place after the advised method completes,
regardless of the outcome.
3. After-returning—The advice functionality takes place after the advised method
successfully completes.
4. After-throwing—The advice functionality takes place after the advised method
throws an exception.
5. Around—The advice wraps the advised method, providing some functionality
before and after the advised method is invoked.

从上面的5种情况来看，AOP能做的事情非常多，而且让我惊讶的是，居然可以在function抛出一个异常之后来执行advice，对于第三点来说，还能看在一个函数结束之后，根据返回值来看一下。

#### Joint Points

An electric company services several houses, perhaps even an entire city. Each house has an electric meter that needs to be read, so each house is a potential target for the meter reader. The meter reader could potentially read all kinds of devices, but to do her job, she needs to target electric meters that are attached to houses.

In the same way, your application may have thousands of opportunities for advice to be applied. These opportunities are known as join points. A join point is a point in the execution of the application where an aspect can be plugged in. This point could be a method being called, an exception being thrown, or even a field being modified. These are the points where your aspect’s code can be inserted into the normal flow of your application to add new behavior.

Joint Points这个概念在一月底的时候，我进行阅读的时候，还是不太好理解的，可能是因为当时home office的缘故，大圆子还在不停的找我玩。

这次看的时候就比较的清楚了，按照上面的比喻，一个电力公司可能会服务若干间房子，当然大一点的电力公司可能服务整个城市。每一个房子都有一个电表可以被读一下，从这个角度来看每一个安装了电表的房子对于抄表员来说都是一个潜在的目标，这个抄表员当然从可能性上来说，她可以把所有的设备都读一遍，但是对于她的工作来说，她主要的目标还是去阅读那些安装在特定房子上的电表。

所以反过来看，一个advice在你的程序种可能找到成千上万的使用的机会点，这些机会点就被称作joint points。一个joint point就是一个程序执行过程种的点，这个点可以让一个aspect plugged in。

所以简而言之，joint point就是一个机会点。

#### PointCuts

It’s not possible for any one meter reader to visit all houses serviced by the electric company. Instead, each one is assigned a subset of all the houses to visit. Likewise, an aspect doesn’t necessarily advise all join points in an application. Pointcuts help narrow down the join points advised by an aspect.

对于一个抄表员来说，几乎是不可能让她一个人把所有的房间的电表都读取一遍的。实际上来说，每个抄表员都会被分配到一个电力公司所服务的房间的子集来读取电表。同样的道理，一个aspect也没有必要把一个应用种所有可能的join points都advise一遍。所以pointcuts帮助把需要真正进行advise的join points的点都筛选出来。

If advice defines the what and when of aspects, then pointcuts define the where. A pointcut definition matches one or more join points at which advice should be woven. Often you specify these pointcuts using explicit class and method names or through regular expressions that define matching class and method name patterns. Some AOP frameworks allow you to create dynamic pointcuts that determine whether to apply advice based on runtime decisions, such as the value of method parameters.

如果说advice定义了在aspect的what和when的话，也就是说advice定义了aspect做什么和什么时候做，那么pointcuts就定义了在什么地方做。一个pointcut的定义，可以匹配一个或者多个join points，而在这些连接点的地方，advice应该被编织进去。

所以看这本书的时候，作者的比喻还是非常有趣的。比如，把advice比喻成一个编织物的装饰，那么AOP的编程更多的时候，是为了把这些装饰物编织到恰当的位置。这个编织的过程，应该是由框架（Spring AOP）或者编译器（AspectJ）来完成的，这样程序员就无需关注这些东西，而是关注自己关注的业务逻辑了。

#### Aspects

When a meter reader starts his day, he knows both what he’s supposed to do (report electricity usage) and which houses to collect that information from. Thus he knows everything he needs to know to get his job done.

An aspect is the merger of advice and pointcuts. Taken together, advice and pointcuts define everything there is to know about an aspect—what it does and where and when it does it.

当一个抄表员开始工作的时候（**WHEN**），他既知道他应该做什么（**WHAT**），也知道在那里做（pointut，aka **WHERE**），那么这个抄表员就知道了一切他需要知道的事情，就可以开始愉快的干活了。

一个Aspect就是Advice和PointCuts的结合。当把两者结合起来的时候，关于一个aspect的所有事情都定义清楚了


#### INTRODUCTIONS（引入）

An **introduction** allows you to add new methods or attributes to existing classes. For example, you could create an **Auditable** advice class that keeps the state of when an object was last modified. This could be as simple as having one method, setLast-Modified(Date), and an instance variable to hold this state. The new method and instance variable can then be introduced to existing classes without having to change them, giving them new behavior and state.

一个引入，允许我们针对一个已经存在的类来增加方法和属性。例如，我们可以创建一个就叫做 Auditable 的 Advice的类，来跟踪一个对象的最后被更改的状态。可以非常简单的实现这个方案，例如在这个advice class中保有一个方法和一个instance var。这个方法和实例变量都可以被引入到现存的类中，而不需要我们去改变他们，从而赋予这个对象新的行为和状态。

#### WEAVING（织入）

Weaving is the process of applying aspects to a target object to create a new proxied object. The aspects are woven into the target object at the specified join points. The weaving can take place at several points in the target object’s lifetime:

织入是把aspects应用到目标对象从而创建一个新的代理对象的过程。切面被织入目标对象，在特定的join points上（所谓特定的Joint Point其实也就是Point Cut了），织入这个动作本身可以发生在目标对象的生命周期的很多点上：

* Compile time—Aspects are woven in when the target class is compiled. This requires a special compiler. AspectJ’s weaving compiler weaves aspects this way.
* Class load time—Aspects are woven in when the target class is loaded into the JVM . This requires a special ClassLoader that enhances the target class’s byte-code before the class is introduced into the application. AspectJ 5’s load-time weaving ( LTW ) support weaves aspects this way.
* Runtime—Aspects are woven in sometime during the execution of the application. Typically, an AOP container dynamically generates a proxy object that delegates to the target object while weaving in the aspects. This is how Spring AOP aspects are woven.

1. 编译时，需要特定的编译器，例如AspectJ的编译器。
2. 类加载时，这个需要特定的class loader
3. 运行时。通常来说，AOP的容器会在weaving的时候，动态的创建一个代理对象，代理到目标对象上。这就是Spring AOP的实现方式
