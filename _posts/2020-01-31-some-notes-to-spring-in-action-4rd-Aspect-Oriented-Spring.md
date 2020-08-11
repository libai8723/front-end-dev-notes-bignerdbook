---
title: "Some notes to Spring in Action 4th edition 4th chapter - AOP" 
date: 2020-01-31
tag: "Spring in Action 4th edition"
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

### Spring's AOP Support

Not all AOP frameworks are created equal. They may differ in how rich their join point models are. Some allow you to apply advice at the field-modification level, whereas others only expose the join points related to method invocations. They may also differ in how and when they weave the aspects. Whatever the case, the ability to create pointcuts that define the join points at which aspects should be woven is what makes it an AOP framework.

并不是所有的AOP框架都是生而平等的。它们的主要的区别在于他们支持的join point的丰富程度。有些框架允许你在field-modification级别应用advice，而有些框架只能提供与方法调用相关的joint points。同时区别也在于这些aspects在什么时候，以怎样的方式被框架weave in。 排除无论林林总总这些区别，创建point cuts并且定义哪些joint points需要被woven in的能力，这个能力让我们讨论的东西成为了一个AOP框架。

Spring’s support for AOP comes in four styles:

* Classic Spring proxy-based AOP
* Pure- POJO aspects
* @AspectJ annotation-driven aspects
* Injected AspectJ aspects (available in all versions of Spring)

spring框架支持4种形式的AOP：

1. 经典的Spring基于proxy的AOP
2. 纯粹的POJO的切面
3. 采用@AspectJ注解驱动的切面
4. 直接注入AspectJ的切面

其实前三种都是Spring关于AOP的不同的实现方式，Spring AOP是基于dynamic proxy实现的，也就意味着只能支持method调用级别的切面。

第一种我们称之为经典的Spring AOP在它出现的时代，无疑是先进的，但是和后来声明式切面，和基于注解驱动的切面比较的时候，就会发现异常的繁琐和难以理解。所以我们不讨论classic Spring AOP

With Spring’s aop **namespace**, you can turn pure POJO s into aspects. In truth, those POJO s will only supply methods that are called in reaction to a pointcut. Unfortunately, this technique requires XML configuration, but it’s an easy way to declaratively turn any object into an aspect.

这里为什么要强调 **namespace**，是因为 namespace是xml中的概念，所以这里说的是基于xml的声名式的切面，确实在这种模式下，POJO之需要提供对应pointcut的相应函数就可以了，剩下的事情就交给XML配置文件了，但是很不幸，很多人不喜欢配置XML，但是实话实说这是一种很简单的把POJO变成切面的方式。

![Spring AOP Runtime Proxy](/front-end-dev-notes-bignerdbook/assets/img/Spring-AOP-Runtime-Proxy.png)

上面的图片很好的说明了Spring中面向切面是怎么实现的。

In Spring, aspects are woven into Spring-managed beans at runtime by wrapping them with a proxy class. As illustrated in figure 4.3, the proxy class poses as the target bean, intercepting advised method calls and forwarding those calls to the target bean.
Between the time when the proxy intercepts the method call and the time when it invokes the target bean’s method, the proxy performs the aspect logic. Spring doesn’t create a proxied object until that proxied bean is needed by the application. If you’re using an **ApplicationContext**, the proxied objects will be created when it loads all the beans from the **BeanFactory**. Because Spring creates proxies at runtime, you don’t need a special compiler to weave aspects in Spring’s AOP.

在Spring中，切面是在运行时的时候被weave into被spring管理的beans中的，这里说weave into beans有点不太贴切，因为实际上是使用一个prxoy类来包裹这个beans。就和上图中描述的一样，这个代理类被暴露出来当作这个目标bean，这个代理类会拦截advised method的调用，并且把这些调用转发到真正的目标bean，在代理类拦截调用和代理类调用它包裹的目标bean的方法之间，这个代理类会执行切面的逻辑。Spring会把创建代理对象的时间延迟直到程序需要这个代理类。如果我们使用的是一个 **ApplicationContext** 那么代理对象会在 **Context** 从 **BeanFactory** 加载所有的Beans的时候创建这些代理类。

@todo 所以我们还是要搞明白BeanFactory和ApplicationContext的关系是什么。

### Selecting join points with pointcuts（使用pointcut来选择joint point）

In Spring AOP , pointcuts are defined using AspectJ’s pointcut expression language.

其实Spring AOP使用的是AspectJ的 pointcut expression language，但是受限于Spring AOP的要求，在Spring中仅仅使用AspectJ的pointcut expression language的一个子集。

Table 4.1 lists the AspectJ pointcut designators that are supported in Spring AOP

AspectJ designator（指示符） | Description
-------- |  -------
args()  |  Limits join-point matches to the execution of methods whose arguments are instances of the given types
args()  |  把joint-point限制在被执行的方法的参数与给定的类型匹配的子集上
@args()  |  Limits join-point matches to the execution of methods whose arguments are annotated with the given annotation types
@args()  |  把joint-point限制在被执行的方法的参数是使用给定的注解来注解的子集上
execution()  |  Matches join points that are method executions
execution()  |  匹配所有的方法执行的join points
this()  |  Limits join-point matches to those where the bean reference of the AOP proxy is of a given type
this()  |  @todo 完全没有看懂this()
target()  |  Limits join-point matches to those where the target object is of a given type
target()  |  按照给定的类型来筛选joint-point
@target() |  Limits matching to join points where the class of the executing object has an annotation of the given type
@target() |  按照执行对象的class有给定的注解来筛选joint point
within()  |  Limits matching to join points within certain types
@within() |  Limits matching to join points within types that have the given annotation (the execution of methods declared in types with the given annotation when using Spring AOP)
@annotation  | Limits join-point matches to those where the subject of the join point has the given annotation

这些指示符不知道是干啥的。因为我没用过aspectJ

继续看第一个AOP的例子吧，上面的这些指示符，早晚有一天都用过一次就知道怎么用了。

我们第一个用到的例子是：

```java
execution(* concert.Performance.perform(..))
```

其中execution是上面表格中提到的指示符，说明我们要选择的是method的执行，来作为pointcut的指示符。

括号里面的表达式也比较简单，第一个 * 表示的是意思是任何返回值，然后concert.Performance是包名称和类名称。

然后后面跟随的是类中间的method的名称，(..)的意思是匹配任何参数的意思，这样的话就是所有的concert.Performance下面的所有名字叫做perform的函数了.

然后在定义PointCut的时候我们使用了@Before的注解,还有@After的注解，但是对应的expression写的都是一模一样的表达式。这样就显得非常的罗嗦了，于是作者就介绍了一个方法，使用@PointCut注解来扩展PointCut Expression的能力：

具体就是下面的代码了：

```java
@Aspect
public class Audience {
    /**
     * The value given to the @Pointcut annotation is a pointcut expression, just like the ones you
     * used previously with the advice annotations. By annotating performance() with @Pointcut in this way,
     * you essentially extend the pointcut expression language so that
     * you can use performance() in your pointcut expressions anywhere you’d otherwise
     * use the longer expression. As you can see, you replace the longer expression in all the
     * advice annotations with performance().
     *
     * The body of the performance() method is irrelevant and, in fact, should be
     * empty. The method itself is just a marker, giving the @Pointcut annotation something
     * to attach itself to
     */
    @Pointcut(value = "execution(* io.github.libai8723.Performance.perform(..))")
    public void performance() {}

    // the same meaning with the longer expression
    @Before("performance()")
    public void silenceCellPhones() {
        System.out.println("Silencing cell phones");
    }

    @Before("performance()")
    public void takeSeats() {
        System.out.println("Taking seats");
    }

    @AfterReturning("performance()")
    public void applause() {
        System.out.println("CLAP CLAP CLAP!!!");
    }
    @AfterThrowing("performance()")
    public void demandRefund() {
        System.out.println("Demanding a refund");
    }
}

```

首先这的 @Aspect表明当前的类是一个且面Aspect，关于@PointCut的注解可以详细的看看上面代码中的注释，就是原书文字的拷贝，这里讲解的还是非常好的。既然写的非常好，那我还是翻译一下吧：

In Audience, the performance() method is annotated with @Pointcut. The value given to the @Pointcut annotation is a pointcut expression, just like the ones you used previously with the advice annotations. By annotating performance() with @Pointcut in this way, you essentially extend the pointcut expression language so that you can use performance() in your pointcut expressions anywhere you’d otherwise use the longer expression. As you can see, you replace the longer expression in all the advice annotations with performance().

在上面的代码中, 我们注解的performance函数使用的是@PointCut注解,给到这个注解的值和之前的代码是相同的.当我们使用这种方式进行注解的时候,我们实际上扩展了point cut expression language, 以至于我们可以使用 performance()在我们其他的需要使用pointcut expression的地方来代替在@PointCut中的至,这样可以让我们少写非常多的同样的注解的代码.

The body of the performance() method is irrelevant and, in fact, should be empty. The method itself is just a marker, giving the @Pointcut annotation something to attach itself to. Note that aside from the annotations and the no-op performance() method, the Audience class is essentially a POJO . Its methods can be called just like methods on any other Java class. Its methods can be individually unit-tested just as in any other Java class. Audience is just another Java class that happens to be annotated to be used as an aspect.

我们可以看到performance函数的body是空的,实际上也应该如此,因为这个body实际上没有任何用途,method本身就是一个标记而已,用来给到@Pointcut这个注解一个可以注解的东西. 我们可以注意到除了这个毫无用途的函数之外,整个Audience类就是一个POJO,它的方法可以像其他java类一样被调用, 这样被单元测试起来也是很方便的. 或者我们应该反过来说,Audience类仅仅是一个POJO, 只是恰好被使用了一个且面的注解进行了注解.

剩下的时间就简单了, 因为现在看的还是SIA 4th的内容, 大部分内容还是需要自己手工去配置的, 不像现在的Spring Boot, 所以采用Java Congifuration的方式, 增加一个配置类:

```java
@Configuration
@ComponentScan
@EnableAspectJAutoProxy
public class ConcertConfig {

    public static void main(String[] args) {
        ApplicationContext ctx = new AnnotationConfigApplicationContext("io.github.libai8723");
        Performance p = (Performance) ctx.getBean("music");
        p.perform();
    }

    @Bean
    public Audience audience() {
        return new Audience();
    }
}
```

这里的@Configuration和@ComponentScan都是老生畅常谈了, 表明自己是一个配置类, 表明要启用自动化的Component搜索, 第三个注解第一次见到  @EnableAspectJAutoProxy据说增加了这个才有用.

然后我看了一下 @EnableAspectJAutoProxy的文档, 解答了我心中的一个问题, 就是书上的代码, 针对Audience这个切面, 在配置类中使用 @Bean 注解 方法的方式来告诉Context需要初始化一个实例, 但是对于Music这个类来说, 却是使用的是在class级别注解一个 @Component 来告诉Context需要初始化一个实例, 前后看着不一致的感觉.

于是在@EnableAspectJAutoProxy的文档中,看到下面的描述:

```java
/*
 * Enables support for handling components marked with AspectJ's @Aspect annotation,
 * similar to functionality found in Spring's <aop:aspectj-autoproxy> XML element.
 * To be used on @Configuration classes as follows:
 */
   @Configuration
   @EnableAspectJAutoProxy
   public class AppConfig {
  
       @Bean
       public FooService fooService() {
           return new FooService();
       }
  
       @Bean
       public MyAspect myAspect() {
           return new MyAspect();
       }
   }

// Where FooService is a typical POJO component and MyAspect is an @Aspect-style aspect:

   public class FooService {
  
       // various methods
   }
   @Aspect
   public class MyAspect {
  
       @Before("execution(* FooService+.*(..))")
       public void advice() {
           // advise FooService methods as appropriate
       }
   }

// In the scenario above, @EnableAspectJAutoProxy ensures that MyAspect will be properly processed and that FooService will be proxied mixing in the advice that it contributes.
// Users can control the type of proxy that gets created for FooService using the proxyTargetClass() attribute. The following enables CGLIB-style 'subclass' proxies as opposed to the default
// interface-based JDK proxy approach.
```

从这个代码可以看出全部都使用@Bean的注解,是可以的. 代码的注释里面也有对应的全都使用全自动扫描的方式的书写方式

```java
    // Note that @Aspect beans may be component-scanned like any other. Simply mark the aspect with both @Aspect and @Component:
   package com.foo;
  
   @Component
   public class FooService { ... }
  
   @Aspect
   @Component
   public class MyAspect { ... }
    // Then use the @ComponentScan annotation to pick both up:
   @Configuration
   @ComponentScan("com.foo")
   @EnableAspectJAutoProxy
   public class AppConfig {
    // no explicit @Bean definitions required
   }
```

上面的代码也是没有问题的, 但是需要在注解@Aspect的类上面增加一个@Component的注解, 执行效果是一样的.

### Creating Around Advice（创建环绕通知）

环绕advice是一种非常强大的advice，允许我们编写完成的逻辑去包裹被advised的函数。我们对之前的例子进行简单的改写来演示环绕通知。

```java
public class Audience {

    /**
     * The value given to the @Pointcut annotation is a pointcut expression, just like the ones you
     * used previously with the advice annotations. By annotating performance() with @Pointcut in this way,
     * you essentially extend the pointcut expression language so that
     * you can use performance() in your pointcut expressions anywhere you’d otherwise
     * use the longer expression. As you can see, you replace the longer expression in all the
     * advice annotations with performance().
     *
     * The body of the performance() method is irrelevant and, in fact, should be
     * empty. The method itself is just a marker, giving the @Pointcut annotation something
     * to attach itself to
     */
    @Pointcut(value = "execution(* io.github.libai8723.Performance.perform(..))")
    public void performance() {}

    // the same meaning with the longer expression
    @Before("performance()")
    public void silenceCellPhones() {
        System.out.println("Silencing cell phones");
    }

    @Before("performance()")
    public void takeSeats() {
        System.out.println("Taking seats");
    }

    @AfterReturning("performance()")
    public void applause() {
        System.out.println("CLAP CLAP CLAP!!!");
    }
    @AfterThrowing("performance()")
    public void demandRefund() {
        System.out.println("Demanding a refund");
    }

    @Around("performance()")
    public void watchPerformance(ProceedingJoinPoint jp) {
        try {
            System.out.println("IAround Advice: Silencing the cell phones");
            System.out.println("In Around Advice: Taking seats");
            System.out.println("before execute the advised method, method signature is: " + jp.getSignature().toLongString());
            jp.proceed();
            System.out.println("In Around Advice: CLAP CLAP CLAP!!!");
        } catch (Throwable e) {
            System.out.println("In Around Advice: Demanding a refund");
        }
    }
}
```

上面的代码，的实际的输出是，这个输出是比较讲究的，可以仔细看一下

```text
IAround Advice: Silencing the cell phones
In Around Advice: Taking seats
before execute the advised method, method signature is: public abstract void io.github.libai8723.Performance.perform()
Silencing cell phones
Taking seats
Piano Solo
CLAP CLAP CLAP!!!
In Around Advice: CLAP CLAP CLAP!!!
```

从代码来看增加一个环绕通知也是非常简单的，使用ProceedingJoinPoint这个参数可以在环绕通知里面调用被advised的函数。

This object is necessary because it’s how you can invoke the advised method from within your advice. The advice method will do everything it needs to do; and when it’s ready to pass control to the advised method, it will call ProceedingJoinPoint’s proceed() method.

**ProceedingJoinPoint jp** 这个参数是必要的，使用这个参数可以让我们在环绕通知里面调用被advised的函数，但我们需要把控制权交给被建议的method的时候我们去调用**proceed()**函数就可以了。

Note that it’s crucial that you remember to include a call to the proceed() method. If you don’t, then your advice will effectively block access to the advised method. Maybe that’s what you want, but chances are good that you do want the advised method to be executed at some point.

虽然这个说法很无聊，但是还是要提醒大家记得去调用 **ProceedingJoinPoint.proceed()** 的函数，因为如果不调用的话，就相当于我们阻止了这个函数的调用。

What’s also interesting is that just as you can omit a call to the proceed() method to block access to the advised method, you can also invoke it multiple times from within the advice. One reason for doing this may be to implement retry logic to perform repeated attempts on the advised method should it fail.

当然在环绕通知里面也不一定仅仅调用一次被advised的函数，当然可以调用多次了，例如在“失败重试”的场景下。

### 处理advice中的参数

