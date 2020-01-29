---
title: "Some notes to <Spring in Action 4th edition> 1st" 
date: 2020-01-26
tag: Spring, IoC
---

## 1. 关于依赖注入(Dependency Injection)

关于什么是DI，为什么要使用DI，在很长一段时间内，我都是不太理解的。然后在前天和昨天我在尝试学习junit3.8的时候，我好像意识到了DI的价值了。

因为我想要写一个基于时间来创建文件夹的小工具的时候，我有一个函数是根据当前的时间，返回一个类似于yyyy_mm_dd_hh_mm_ss的字符串，我希望针对这个函数来写一个单元测试的函数。

但是我发现这是一个比较困难的事情，因为这个测试会依赖于当前的系统的时间，所以在使用junit里面的Assert里面的assertEquals函数的时候，我是没办法设置当前的时间的。于是我去google上检索，看看依赖于current date的函数要怎么测试呢？

StackOverflow上的一些意见是说，应该把当前的时间，作为函数的参数，这样的话，我们就可以把时间作为参数传入了，然后我们仅仅需要测试的是函数是否可以把输入的时间变量，正确的转化为上面的格式化的字符串。

于是我突然明白了，这里的函数其实是依赖于当前的时间的，把依赖的东西搞成函数的参数，或者是搞成构造函数的参数，或者是当成是类的属性，其实是有助于分离这个依赖的，因为我自己写的函数中，没有直接硬编码为对应的时间变量的依赖。

对于时间的测试的方法，网上非常多的人推荐使用java.time包下面的类和接口来表达，有助于完成单元测试。这里后面再说吧。

于是我又想起来一个问题，就是虽然使用构造器，setter函数把依赖限制到了函数接口上，但是最终还需要一个class来完成这些函数的调用，其实也就是说，需要把一个class依赖的其他对象，注入到这个class中，所以我突然明白了，还是需要学以下DI的，既然要学DI了，那就学Spring的吧，毕竟是最流行的DI框架。

## 2. 对于Spring Library的选择

为了完成最基本的DI的演示（Spring in Action 第三章的演示说明），那么我依赖的是什么库呢？在maven的中心仓库搜了一下，发现相关的应该是 Spring context或者是Spring core，那么使用哪一个呢？google之后的结果是这么说的，Spring Context是依赖于spring-aop，spring-beans，spring-core，spring-expression的，所以只需要在pom中引入spring-context即可。

至于这几个artifact是干啥的，后面再看吧。

于是下面这个就是cdplayer这个项目当前的pom文件结构了：[pom.xml at 095fd9e](https://github.com/libai8723/cdplayer/blob/095fd9eb2e5dce8b1911da6de5e9cf9075345c5b/pom.xml)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>io.github.libai8723</groupId>
  <artifactId>cdplayer</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>cdplayer</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
    <java.version>1.8</java.version>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

  <dependencies>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.3.RELEASE</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.2.3.RELEASE</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>com.github.stefanbirkner</groupId>
      <artifactId>system-rules</artifactId>
      <version>1.19.0</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

可以看到这里主要依赖的就是spring-context，还有一个叫做spring-test，这个还是很有说头的。

其实截止到目前这个工程都是很简单的，因为只有几个类参与其中了，定义来2个接口：MediaPlayer和CompactDisc。

然后CDPlayer implements MediaPlayer，tobeContinued implements CompactDisc。

然后在CDPlayerConfig这个类里面，啥都没写，只写了@Configuration和@ComponentScan两个注解，据说这样子就会开启Spring的context的自动检索component的功能了。

然后在CDPlayer的类上写了@Component注解，在它的constructor上写了@AutoWired注解，这样Spring Context就会自动去寻找符合要求的CompactDisc的实现了。

最后最重要的其实是main类，我是搞了一段时间之后才明白的，要怎用，具体如下：

[main.java at 1e031c3](https://github.com/libai8723/cdplayer/blob/1e031c3e7f9c3de08e67cb1e7fd0328bc13b6816/src/main/java/io/github/libai8723/Main.java)

```java
package io.github.libai8723;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args){
        ApplicationContext ctx = new AnnotationConfigApplicationContext("io.github.libai8723");

        MediaPlayer mp = (MediaPlayer) ctx.getBean("cdPlayer");

        mp.play();
    }
}
```

在这里可以看到，使用了一个叫做AnnotationConfigApplicationContext的上下文，要求是在io.github.libai8723下面来搜索对应的注解来解析具体的配置。其实这一句完成之后，对应的类的初始化和Wire工作就做完了。那么我们就找到cdPlayer这个实例，开始播放就行了。就是这么简单

## 3. 说说测试吧

现在这个时间点上再看这个单元测试的类，就感觉好多了，上一次看这个东西，我记得还是2017年的时候吧，在老婆公司的会议室，看了半天，反正能运行了，但是看不懂，下面是代码，写了很多很有感想的注释

[CDPlayerTest at 095fd9e](https://github.com/libai8723/cdplayer/blob/1e031c3e7f9c3de08e67cb1e7fd0328bc13b6816/src/test/java/io/github/libai8723/CDPlayerTest.java)

```java
package io.github.libai8723;

/**
 * 现在再来看这个SIA 4th中的开篇的例子就感触多了很多了
 * SpringJUnit4ClassRunner是一个Spring框架提供的Runner，按照junit3的Test infected的文章来说，runner主要的作用是提供环境的
 * 在这个例子中其实SpringJUnit4ClassRunner就是一个Runner，这个Runner提供的主要作用就是创建一个Spring Application Context
 * 因为所有的Spring程序都需要在Spring Application Context中才能运行，就算是单元测试函数也是一样的
 */

import static org.junit.Assert.*;

// 这里的org.junit.Rule配合下面的StandardOutputStreamLog使用简直绝了，可以捕获向System.out中输出的东西，用作测试
import org.junit.Rule;
import org.junit.Test;
import org.junit.contrib.java.lang.system.SystemOutRule;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

//RunWith是Junit4的一个注解，现在看来就是说明使用什么Runner来运行这些测试类
@RunWith(SpringJUnit4ClassRunner.class)
//ContextConfiguration是spring的test.context中的注解，指明配置类在哪里
@ContextConfiguration(classes=CDPlayerConfig.class)
public class CDPlayerTest {
    @Rule
    public final SystemOutRule systemOutRule = new SystemOutRule().enableLog();

    @Autowired
    private MediaPlayer player;

    @Autowired
    private CompactDisc cd;
    @Test
    public void cdShouldNotBeNull() {
        assertNotNull(cd);
    }

    @Test
    public void playCorrectCD() {
        player.play();
        assertEquals("Playing Stefanie Sun Yanzi's To be Continued...\n", systemOutRule.getLogWithNormalizedLineSeparator());
    }
}
```

感受是什么呢，上面写的比较清楚了，首先是@RunWith注解，说明了使用什么Runner来运行。自从读了Test Infected这个文章之后，发现明白什么是runner了，其实就是一个运行器，目前的运行器大部分都是用反射的机制，来检测test目录下面有哪些测试类，以及类里面有那些测试的方法需要被运行，运行过程中会收集运行的结果是否正确。

按照文章的说法，Runner主要是提供测试类运行的环境的，所以对于Spring的程序来说，其实就是提供context了。

然后就是@ContextConfiguration了，这个注解表明了，我们要使用哪一个类来作为Spring Context的配置类，这里书上的作者应该是为了偷懒，就使用了src里面的配置类，侧面也说明了，其实在Test的情况下，我们可以单独搞一个配置类，给被测试的场景，注入不同的于src内的依赖场景，这样还是不错的。

然后就是@Rule这个注解，这个是junit4的一个注解了，非常有用，但是暂时先不看了。

@AutoWired，就无需多说了，context会自动找到对应需要的类，初始化bean，并且注入依赖。

@Test也没啥好说的了，就是测试method了，会被runner发现，并且执行，且记录执行的结果的。
