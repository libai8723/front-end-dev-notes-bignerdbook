---
title: "Some notes to Spring in Action 4th edition 2nd" 
date: 2020-01-26
tag: "Spring in Action 4th edition"
---

## Advanced Wiring

其实在第二章中，已经讲了很多关于wiring的技巧了，但是第三章要将更多更高级的技巧。

### Environments and profiles

在软件开发过程中最具有挑战性的工作之一，就是把开发好的软件从一个环境transition到另外一个环境中。为什么会出现这种transition呢，那是因为我们总是需要把开发环境的软件转换到生产环境中，在软件工程中我们把这个叫做deployment。

Certain environment-specific choices made for development aren’t appropriate or won’t work when the application transitions from development to production.

针对开发环境来选定的一些抉择，对于生产环境来说可能并不合适，甚至都不能生效

Database configuration, encryption algorithms, and integration with external systems are just a few examples of things that are likely to vary across deployment environments.

数据库的配置，加密的算法，与外部系统的集成，仅仅是在不同环境之间切换的时候，最可能发生变化的例子而已，其他的还会有非常多。

然后这个时候，作者举了一个例子，例如在一个开发用的环境中，我们可能会使用一个embedded database preloaded with test data. For example, in a Spring configuration class, you might use EmbeddedDatabaseBuilder在一个被标记为@Bean的方法中，如下：

```java
@Bean(destroyMethod="shutdown")
public DataSource dataSource() {
return new EmbeddedDatabaseBuilder()
    .addScript("classpath:schema.sql")
    .addScript("classpath:test-data.sql")
    .build();
}
//值得一提的是，EmbeddedDatabaseBuilder是org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder的一个类，用来构建一个嵌入式的database，方便大家进行开发
```

然后作者提到了说，尽管上面这个嵌入式的数据库对于开发环境来说是足够了，但是对于生产环境来说是horrible的，不懂为啥是horrible的。但是理由是这样的，In a production setting, you may want to retrieve a DataSource from your container using JNDI. 在生产环境配置中，你可能希望从你的容器中使用JNDI的方式来retrieve一个DataSource，所以这里我没搞懂DataSource是什么，难道又是另外一种类似JMS的J2EE的API标准吗？看着非常有可能：

下面是使用JNDI的方式，从container中获取一个DataSource的代码，看起来可以有一些参考意义：

```java
@Bean
public DataSource dataSource() {
    //从名字来看，创建了一个JndiObject的FactoryBean
    JndiObjectFactoryBean jndiObjectFactoryBean =
        new JndiObjectFactoryBean();
    //指定了Jndi的名字
    jndiObjectFactoryBean.setJndiName("jdbc/myDS");
    jndiObjectFactoryBean.setResourceRef(true);
    //这里的ProxyInterface，难道是Java的Proxy设计模式吗？
    jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
    return (DataSource) jndiObjectFactoryBean.getObject();
}
```

这一段说的还是比较明细的，使用Jndi retrieve data source的好处，Retrieving a DataSource from JNDI allows your container to make decisions about how it’s created, including handing off a DataSource from a container-managed connection pool.

应该是这么翻译的，使用JNDI检索DataSource的方式，允许你的container来决定数据源是怎么被创建的，包括从一个由容器管理的链接池中移交一个DataSource。

但是对于开发来说，使用一个Jndi管理的DataSource对于开发来说，有点过于复杂，太过了。

除了上面两种情况下，其实还有另外一种叫做QA environment的环境，猜测是用来做质量保证的，不知道是干啥的，难道是做压测，安全验证？总之在这种环境下，你可能希望链接到另外一种数据源：

```java
@Bean(destroyMethod="close")
public DataSource dataSource() {
    BasicDataSource dataSource = new BasicDataSource();
    dataSource.setUrl("jdbc:h2:tcp://dbserver/~/test");
    dataSource.setDriverClassName("org.h2.Driver");
    dataSource.setUsername("sa");
    dataSource.setPassword("password");
    dataSource.setInitialSize(20);
    dataSource.setMaxActive(30);
    return dataSource;
}
```

从上面的代码来看，应该是大家都链接到某一个同一个DB，感觉是h2的数据库。

所以我们的目标也很简单，就是针对不同的environment，配置不同的DataSource的Bean.

为了达到这个目标，其中一种方式是用Maven profiles。

One way of doing this is to configure each bean in a separate configuration class (or XML file) and then make a build-time decision (perhaps using Maven profiles) about which to compile into the deployable application.

这个方式的要点是，在build的时候，决定把哪一个配置类compile并且打包进去。但是这个方法的问题点也很清楚，就是需要针对不同的environment，进行re-build，也就是说，不同的环境build出来的包是不一样的。

对于这个问题，Spring提供了解决方案，叫做

### Configuring profile beans

Spring’s solution for environment-specific beans isn’t much different from build-time solutions. Certainly, an environment-specific decision is made as to which beans will and won’t be created. But rather than make that decision at build time, Spring waits to make the decision at runtime. Consequently, the same deployment unit (perhaps a WAR file) will work in all environments without being rebuilt.

其实Spring的针对不同环境的解决方案和build-time的解决方案，也没有什么太大的不同。它们都是需要针对不同的环境，做出一个决定，看看那些bean需要被创建，哪些不能被创建。但是spring把这个动作从build-time推迟到了run-time的时候来做决定。这样的话，同一个 deployment unit（jar，war或者tar.gz）就可以在不同的环境之间被使用了，而不需要重新re-built

从Spring 3.1开始，引入了一个叫做bean profiles的东西，为了使用profile，我们需要这么干：

you must gather all the varying bean definitions into one or more profiles and then make sure the proper profile is active when your application is deployed in each environment.

也就是说我们需要把和环境变化有关系的beans，都收集到一个或者若干个profile中，然后在部署的时候，激活特定的profile。

如果我们使用java configuration的配置方式的话，那我们主要就是在使用@Profile这个注解，来说明对应的配置类中的Bean属于哪一个Profile

例子如下：

```java
package com.myapp;
import javax.activation.DataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType;

@Configuration
@Profile("dev")
public class DevelopmentProfileConfig {
    @Bean(destroyMethod="shutdown")
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.H2)
        .addScript("classpath:schema.sql")
        .addScript("classpath:test-data.sql")
        .build();
    }
}
```

从这里我们可以看出，当前这个是针对dev环境的配置。这样会产生至少2~3个配置类吧，因为不同的环境，需要不同的@Profile的注解

从Spring 3.2开始 @Profile可以和@Bean注解一起，用在method上了，这样就可以把不同的profile配置在同一个配置类中了，如下：

```java
package com.myapp;
import javax.activation.DataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType;
import org.springframework.jndi.JndiObjectFactoryBean;

@Configuration
public class DataSourceConfig {
    @Bean(destroyMethod="shutdown")
    @Profile("dev")
    public DataSource embeddedDataSource() {
        return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.H2)
        .addScript("classpath:schema.sql")
        .addScript("classpath:test-data.sql")
        .build();
    }

    @Bean
    @Profile("prod")
    public DataSource jndiDataSource() {
        JndiObjectFactoryBean jndiObjectFactoryBean =
            new JndiObjectFactoryBean();
        jndiObjectFactoryBean.setJndiName("jdbc/myDS");
        jndiObjectFactoryBean.setResourceRef(true);
        jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
        return (DataSource) jndiObjectFactoryBean.getObject();
    }
}
```

这样看，还是很方便的，但是还是没懂，怎么指定当前的部署要使用哪一个profile。

当然，因为Spring之前就是通过xml文件来进行配置的，所以当然也有方法，用xml的方式来配置不同的profile。关于xml的配置，其实还是有非常多的可说道的地方的。这里就不细说了。

终于看到我期待的问题了，That raises the question: how do you make a profile active?

How do you make a profile active?

### Activating profiles

Spring honors two separate properties when determining which profiles are active: **spring.profiles.active** and **spring.profiles.default**. If spring.profiles.active is set, then its value determines which profiles are active. But if spring .profiles.active isn’t set, then Spring looks to spring.profiles.default. If neither spring.profiles.active nor spring.profiles.default is set, then there are no active profiles, and only those beans that aren’t defined as being in a profile are created.

有下面的6种不同的方式来设置这两个属性：

1. As initialization parameters on DispatcherServlet
2. As context parameters of a web application
3. As JNDI entries
4. As environment variables
5. As JVM system properties
6. Using the @ActiveProfiles annotation on an integration test class

然而，我就知道环境变量，JVM系统属性，其他的都没用过啊。据说DispatcherServlet是Spring MVC的一个核心的设计模式。2，3，6，都不知道是什么了，后面继续看吧。

然后作者给了我们一个他喜欢的实践方法：

One approach that I like is to set spring.profiles.default to the development profile using parameters on DispatcherServlet and in the servlet context (for the sake of ContextLoaderListener). For example, a web application’s web.xml file might set spring.profiles.default as shown in the next listing.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/root-context.xml</param-value>
    </context-param>
    <context-param>
        <param-name>spring.profiles.default</param-name>
        <!--注意这里配置了context-param-->
        <param-value>dev</param-value>
    </context-param>
    <listener>
        <listener-class>
org.springframework.web.context.ContextLoaderListener
</listener-class>
    </listener>
    <servlet>
        <servlet-name>appServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <!--注意这里配置了servlet的param-->
            <param-name>spring.profiles.default</param-name>
            <param-value>dev</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>appServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

上面是一个基于servlet的web-app的xml文件的配置，我们可以看到6种方法的第二种，context parameter of a web application，和第一种，initialization parameters on DispatcherServlet。而且配置的都是spring.profiles.default的值，而且配置的都是dev的取值。

这样的好处是，任何开发人员的话，都可以直接从cvs系统中获得代码之后，直接在本地使用开发配置来运行程序，例如使用一个embedded database来运行，而不需要额外的一些配置才能运行程序。

然后，等需要把程序部署到QA环境，或者是pro环境的时候，负责部署的人就可以设置 spring.profiles.active这个变量，当然可以通过其他的方式，例如system properties，environment variable，JNDI entries等等。并且当spring.profiles.active被设置的时候，spring.profiles.default就被忽略了，这样在生产环境或者QA环境，对应的产生环境或者QA环境的bean就生效了。

上面讲的都是开发环境，QA环境和生产环境，下面作者提了一句，关于使用profiles进行测试的

When running an integration test, you’ll often want to test using the same configuration (or some subset thereof) you’d use in production. But if your configuration references beans that are in profiles, you need a way to enable the appropriate profile when running those tests.

当运行一个集成测试（感觉这里说的集成测试的意思是，只要多个Bean被彼此注入的依赖关系的时候，就算是集成测试了）的时候，你经常是希望可以使用和生产环境相同的配置。但是你的很多Bean都是在特定的profile中的，这时候你需要一个办法来让特定的profile生效，从而来运行这些测试。

Spring offers the @ActiveProfiles annotation to let you specify which profile(s) should be active when a test is run. Often it’s the development profile that you’ll want to activate during an integration test. For example, here’s a snippet of a test class that uses @ActiveProfiles to activate the dev profile:

Spring提供了一个叫做 @ActivateProfiles的注解来让我可以指定，在运行测试用例的时候，生效哪一个profile。例如下面的测试用例中，就让dev的profile生效了

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes={PersistenceTestConfig.class})
@ActiveProfiles("dev")
public class PersistenceTest {
...
}
```

总结以下，Spring profiles是一个非常棒的方式来通过profile的条件来决定哪些beans被创建和使用，但是Spring除此之外，还提供了其他的更通用方式来让你通过自定义条件的方式来决定哪些bean被创建和使用。

### Conditional Bean

在阅读这一章节之前，我总感觉既然可以使用Java Configuration的配置类的方式来生成Bean，那么Conditional这种事情，直接在对应生成Bean的method里面写逻辑不就行了吗。

这里提出的场景是，这样描述的：

1. Suppose you want one or more beans to be configured if and only if some library is available in the application’s classpath.
2. Or let’s say you want a bean to be created only if a certain other bean is also declared.
3. Maybe you want a bean to be created if and only if a specific environment variable is set.

感觉第二和第三个还是可以想象的到的，比如第二个，我们可以在context中检测是否有特定的Bean被创建了。比如第三个，我们可以直接检测环境变量。但是第一个，好像是和reflection有关系的，这里我掌握的不熟。暂时想不到办法

从Spring 4开始，引入了一个新的注解  @Conditional的注解，这个注解可以被用到被@Bean注解的method。如果对应的@Conditional中的条件为真的时候，这个对应的Bean就会被创建了。

下面的就是对应的method的例子

```java
@Bean
@Conditional(MagicExistsCondition.class)
public MagicBean magicBean() {
    return new MagicBean();
}
```

可以看到@Conditional中的类，MagicExistsCondition类，这个类需要一个叫做Condition的interface：

```java
public interface Condition {
    boolean matches(ConditionContext ctxt,
        AnnotatedTypeMetadata metadata);
}
```

其实这个interface也是很简单的，只需要implement一个叫做matches的方法即可，下面就是检测环境变量中是否存在一个叫做magic的环境变量，从而决定是否创建一个叫做magic的Bean的代码：

```java
package com.habuma.restfun;
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;
import org.springframework.util.ClassUtils;

public class MagicExistsCondition implements Condition {
    public boolean matches(ConditionContext context,
        AnnotatedTypeMetadata metadata) {
        Environment env = context.getEnvironment();
        return env.containsProperty("magic");
    }
}
```

看到上面最关键的代码是context.getEnvironment()，不知道这个和System.getenv()有什么区别。

上面的代码中matches的2个参数非常有说道的，前者可以用来访问context，后者主要用来访问当前的方法的注解的信心。

从Spring 4开始，@Profile这个注解，实际上是基于@Conditional的注解来写出来的。所以前者参数可以查看环境和上下文，后者可以用来看对应的方法的注解信息。

其实看到这里，就和书上的后面提到的Custom Annotation遥相呼应了，因为后面提到Spring里面经常使用Customer Annotation来进行扩展的。说白了，其实@Profile就是使用@Conditional的注解写出来的。看来是时候有必要好好看以下Java Annotation的说明了，好好了解一下什么是注解，以及用途是啥。

### Addressing ambiguity in autowiring

上面这个标题的翻译是，解决autowiring中的歧义。这一章节说明了2种方法，一个是设定主要的Bean，另外一个是使用限定词，也就是qualifier的方式。

在前面的第二章中，我们使用了spring的autowiring的特性来，把所有的wiring的工作都交给了Spring框架来完成，autowiring是一个非常棒的特性，因为：

Autowiring is a huge help because it reduces the amount of explicit configuration necessary to assemble application components.

autowiring极大的减少了我们显示的配置的代码，在组装应用程序的组件的时候。

但是我们知道autowiring在仅仅只有一个Bean符合条件的时候才会生效，当有超过一个Bean符合条件的时候，Spring就不知道如何 autowiring the property, constructor argument or the method parameter. 因为autowiring主要就是标注在属性，构造函数参数和setter method上面的。

书上说，当出现超过一个符合条件的Bean的时候就会抛出异常，因此我加了一个新的CompactDisc的实现，并且标记为@Component了，果然重现了这个问题：

可以在这个提交上看到这个问题 [NoUniqueBeanDefinitionExceptionExample](https://github.com/libai8723/cdplayer/tree/39a6dc67ede3ab266e3308afec79e020be1cab1a)

本地的抛错也是比较明显的：

```cmd
一月 30, 2020 1:14:26 下午 org.springframework.context.support.AbstractApplicationContext refresh

警告: Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'cdPlayer' defined in file [C:\Users\Q\Desktop\cdplayer\target\classes\io\github\libai8723\CDPlayer.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type 'io.github.libai8723.CompactDisc' available: expected single matching bean but found 2: kite,tobeContinued

Exception in thread "main" org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'cdPlayer' defined in file [C:\Users\Q\Desktop\cdplayer\target\classes\io\github\libai8723\CDPlayer.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type 'io.github.libai8723.CompactDisc' available: expected single matching bean but found 2: kite,tobeContinued
```

上面就是抛出错误的一个例子了。

解决的办法也是很简单的，比如我们可以 Designating a primary bean

我们可以在其中一个CompactDisc的实现类上标记一个@Primary的注解，来向Spring说明，这个实现类才是主要的，首选的。但是当你标记2个实现类都是@Primary的时候，Spring还是不知所措，所以这个时候，我们要采用一个叫做 Qualifying autowiring beans的方式

其实说白了，也就是在实现类上使用 @Qualifier注解，具体的例子，见下方：

```java
@Autowired
@Qualifier("iceCream")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

可以看到，我们仅仅在setter方法上面使用了@Qualifier的注解，并且传递了一个叫做"iceCream"的字符串参数。这种使用方式，是qualifier的最简单的使用方式。

上面的代码之所以可以工作，是有一定的巧合的，因为严格来说"iceCream"叫做一个限定词，Spring框架会根据这个限定词去找拥有对应的限定词的Bean。

而IceCream的类上，刚好标记了一个没有参数的@Component的注解，对于没有参数的@Component注解标记的Class来说，它们会被Spring创建出一个Bean，这个Bean的ID就是类名的首单词字母的小写，所以iceCream就是这个Bean的ID了。

但是对于IceCream这个类来说，它并没有被@Qualifier注解标记，所以Spring框架就会给这些没有@Qualifier标记的Bean一个默认的qualifier，也就是等于这个Bean的ID。所以上面的代码，在消除歧义的时候就会生效了。

上面的代码很简单的，基于默认的Bean ID的qualifier的方法是有局限性的，比如当我们决定把IceCream这个类重构类名的时候，对应的setter方法里面，因为是一个String类型的Qualifier描述，就会有问题了，因为找不到对应的Qualifier了，所以使用默认的Bean ID作为qualifier的方式，在重构类名的时候，是非常危险的。

使用一个专业的描述方法来说，上面的setter方法setDessert()方法与IceCream的类名紧耦合在一起了。任何对于类名的重构，都会导致不可用。

最佳的实践是 Creating Custom Qualifier. 这种方式下，我们仅仅需要把 @Qualifier注解标记在 @Component标记的类上，或者是把@Qualifier注解标记在 @Bean标记的Java Configuration method上，并且Qualifier一个字符串，作为自定义的qualifier。

比如下面的例子

```java
@Component
@Qualifier("cold")
public class IceCream implements Dessert { ... }

//在@Autowired标记的函数上使用对应的@Qualifier注解
@Autowired
@Qualifier("cold")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

而且对于自定义qualifier的最佳实践是这么说的，对于Bean，使用其特性或者描述性的词汇，对于IceCream这种Dessert来说，cold就是IceCream作为Dessert的特性。

并且在语义上也很有趣，在injection point的时候，可以读作“give me the code Dessert”

自定义注解：Self Defined Annotation。上面的方法是说，当有2个比较冷的甜点的时候，大家可能会想，使用多个重复的@Qualifier注解来描述不同的特性，比如对于IceCream来说，这两个特性是cold和creamy，对于Popsicle来说，这两个特性是cold和fruity。

但是不可行，因为直到Java 8版本，Java才允许针对同一个item标记多个相同的注解，也就是说Java 8之前都不行，为了解决这个问题，大家可以创建custom qualifier。

创建Custom Qualifier也是非常简单的：

All you have to do is create an annotation that is itself annotated with @Qualifier. Rather than use @Qualifier("cold"), you can use a custom @Cold annotation that’s defined like this:

```java
@Target({ElementType.CONSTRUCTOR, ElementType.FIELD,
ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Cold { }


@Target({ElementType.CONSTRUCTOR, ElementType.FIELD,
ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Creamy { }
```

这样，你就可以在Class和setter Method上直接使用 @Cold和@Creamy的注解了，效果是一样的。

这种创建custom annotation的方式，在Spring中是非常常见的一种做法。
