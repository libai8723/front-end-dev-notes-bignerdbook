---
title: "Some notes to Spring in Action 4th edition 4th chapter 3.4 Section- Annotating introduction" 
date: 2020-09-19
tag: "Spring in Action 4th edition"
---

## Annotating introductions

对于有些编程语言来说，例如Ruby和Groovy这种，有一种叫做开放性类（open class）的说法。这种东西的存在让我们可以在不改变原有的对象和类型的情况下增加一些新的方法。但是很不幸的是，java没有那么的动态，一旦一个类被编译出来了，那么我们能做的事情就很少了。

但是如果你仔细思考一下这个事情，你会发现在最近的这一章中我们使用aspect不就是在干这个事情吗。当然我们做的事情并没有给已经存在的类添加新的方法，我们仅仅是在已经存在的方法周围增加一些功能。如果一个切面可以包装现有的方法来实现一些额外的功能的话，那为什么不能添加一些新的方法到对象上。而实际上来看，这个给已有的对象和类增加功能的概念，在AOP中被叫做“引入”，aspects可以附加新的方法到Spring的Beans

Recall that in Spring, aspects are proxies that implement the same interfaces as the beans they wrap. What if, in addition to implementing those interfaces, the proxy is also exposed through some new interface? Then any bean that’s advised by the aspect will appear to implement the new interface, even if its underlying implementation class doesn’t. Figure 4.7 illustrates how this works.

让我们回忆一下，在Spring中，切面实际上就是一些代理，这些代理实现了和它们包裹的beans所具备的相同的接口。那么让我们来假设一下，除了实现这些接口之外，这个代理同时还通过一些新的接口来暴露出来一些能力。这样的话，任何被aspect所包裹的bean，都看起来好像是实现了那个新的接口，尽管在这个接口之下的实现的类并没有这个功能。下图中的4.7作了一个示意图来方便理解

![Annotation Introduction](/front-end-dev-notes-bignerdbook/assets/img/SpringInActionChapter4-3AnnotationIntroduction.png)

Notice that when a method on the introduced interface is called, the proxy dele-
gates the call to some other object that provides the implementation of the new
interface. Effectively, this gives you one bean whose implementation is split across
multiple classes.

注意，当被引入的interface上的方法被调用的时候，这个代理会把对应的方法调用的请求分发到其他对应的对象上，这个对象提供了这个新的接口的对应的方法的实现；通过这种方式，给我们了一种把一个bean的实现，分割到若干个classes上的能力。

为了演示这个introduction的功能怎么落地实现的话，我们看一下代码怎么实现吧。

首先，来回忆一下之前在写AOP的简单的例子的时候的类

首先是我们定义了一个叫做Performance的接口：

### Performance Interface

[Performance.java](https://github.com/libai8723/libai8723-Chapter4-AOP-SIA/blob/master/src/main/java/io/github/libai8723/Performance.java)

这个接口被定义在一个叫做Concert的包里面，表明场景是大家在音乐厅听音乐的意思，而音乐厅可能有很多种类的音乐，他们的共性是大家都可以表演，也就是这个Performance类：

```java
public interface Performance {
    void perform();
}
```

### Music implement Performance

然后我们定义了一个Performance的implementation，叫做Music类：
[Music.java](https://github.com/libai8723/libai8723-Chapter4-AOP-SIA/blob/master/src/main/java/io/github/libai8723/Music.java)

```java
package io.github.libai8723;

import org.springframework.stereotype.Component;

@Component
public class Music implements Performance {
    @Override
    public void perform() {
        System.out.println("Piano Solo");
    }
}
```

从这个类可以看出来，除了实现了Performance接口之外，也没有什么特别的东西了。@Component这个注解是用来提示Spring的自动扫描来发现这个Music是一个组件。

然后我们定义了一个叫做Audience的类，这个类将作为一个Aspect，所以这里的语法比较复杂，我们稍微简化一点：
[Audience.java](https://github.com/libai8723/libai8723-Chapter4-AOP-SIA/blob/master/src/main/java/io/github/libai8723/Audience.java)

```java

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;

@Aspect
public class Audience {
    @Pointcut(value = "execution(* io.github.libai8723.Performance.perform(..))")
    public void performance() {}

    // the same meaning with the longer expression
    @Before("performance()")
    public void silenceCellPhones() {
        System.out.println("Silencing cell phones");
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

从这个函数从我们可以看出来，我们定义了一个叫做PointCut的函数，为了方便后面的书写。还给出了一个Before和一个Around的函数，其中Around是比较有意思的。

截至目前为止，我们都是在函数执行前、后来执行一个函数，但是本质上还不是给这个被Advised的对象来添加能力，所以后面要介绍的 introduction 是给被advised的对象来添加一个能力。

然后，我们看一下，按照书上给出的不太好看的例子：

![Annotation Introduction](/front-end-dev-notes-bignerdbook/assets/img/SpringInActionChapter4-3AnnotationIntroduction.png)

这个图还是有必要在这里强调一遍的：

这个图表达的意思是，当外部的caller调用一个被引入的interface定义的method的时候，proxy会把这个调用代理到实现这个method对应的implementation上。

让我们假设，我们需要把一个叫作 [Encoreable](#encoreable)定义的接口引入到任何一个[Performance](#performance-interface)的实现上


### Encoreable

Encoreable的英文的意思就是再来一次的意思。

```java
package concert;
public interface Encoreable {
    void performEncore();
}
```

为了引入Encoreable所以，这里还是要定义一个encoreable的实现的，具体如下：

```java
package io.github.libai8723;

public class EncoreableImpl implements Encoreable{
    @Override
    public void performEncore() {
        System.out.println("Very Good! Please perform again!!!!");
    }
}
```

然后怎么把这个实现引入到performance的实现呢：还是使用一个@Aspect

```java
package io.github.libai8723;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.DeclareParents;

@Aspect
public class EncoreableIntroducer {
    @DeclareParents(value = "io.github.libai8723.Performance+", defaultImpl = EncoreableImpl.class)
    public static Encoreable encoreable;
}
```

从上面可以看到引入一个introduction和引入普通的aspect没有什么区别。

有区别的地方是，普通的aspect，我们使用@Before，@After来在对应的method执行的前后来执行切面的代码，但是在introduction中，我们使用  @DeclareParents来说明，那些Interface需要引入这个能力

@DeclareParents的value属性说明对应的接口，这里的加号表示Performance的子类也需要引入。

@DeclareParents的defaultImpl属性指明了对应的实现引入interface的实现类

类声明中的静态变量配合上@DeclareParents注解，说明了我们要引入的接口。

然后我们看一下主类

```java
package io.github.libai8723;


import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.*;

@Configuration
@ComponentScan
@EnableAspectJAutoProxy
public class ConcertConfig {

    public static void main(String[] args) {
        ApplicationContext ctx = new AnnotationConfigApplicationContext("io.github.libai8723");
        Performance p = (Performance) ctx.getBean("music");
        Encoreable encoreable = (Encoreable) p;
        p.perform();
        encoreable.performEncore();
    }

    @Bean
    public Audience audience() {
        return new Audience();
    }

    @Bean
    public EncoreableIntroducer encoreableIntroducer() {
        return new EncoreableIntroducer();
    }
}
```

从这里我们可以看到，首先我们还是使用@ComponentScan注解来对包下面的所有的component进行扫描，所以这个时候可以用music的名字来取到对应的music的bean，但是因为我们引入了Encoreable的接口，所以这个Bean完全可以被强转为Encoreable类型，然后调用performanceEncore的方法。

这个就是introduction了