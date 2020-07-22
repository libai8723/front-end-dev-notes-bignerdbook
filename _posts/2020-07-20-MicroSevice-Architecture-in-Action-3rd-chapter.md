---
title: "微服务架构实战-第三章" 
date: 2020-07-20
tag: "微服务架构实战"
excerpt_separator: <!--more-->
---

微服务架构实战第三章主要介绍Spring boot和集成其他开源三方件的便利之处，所以才会有这个标题
<!--more-->

## 第三章 微服务之Spring Boot

为什么从第三章开始写呢，因为去桂林的时候在火车上快速的翻书翻了一遍，对应的第一章完全是综述和介绍性的章节，第二章完全是关于设计原则（Design Principle）级别的东西，所以这个东西对不齐下个月的汇报和总结阿，所以我们要快速的看完这本书。

其实在Spring in Action的书籍中，我们已经知道了Spring的一些不太让人开心的事情，那就是大量的xml配置文件的工作，但是从Spring in Action 4th edition 到 5th edition来看，并不是所有的xml配置文件都可以被取消，因为有些时候为了完成AOP的设置，而且是在第三方类库上完成AOP的配置的。所以总结来说，大量的配置工作可以被取代，但是有些无法取代的东西，还是在那边，所以看一下4th的版本，有一个大概的发展历程的了解是非常有好处的。

原书45页开始使用STS来进行第一个案例的教学，因为公司使用的是Intellij的IDE,所以咱们开始使用Intellij的IDE吧，

![创建一个新的SpringBoot的应用，使用Intellij](/front-end-dev-notes-bignerdbook/assets/img/CreateAnNewSpringBootProject.png)

在经过昨天晚上的一系列的折腾之后，发现用intellij来创建spring boot的工程有点曲折，可能是因为 [start spring io](https://start.spring.io) 的网站访问比较慢，或者是因为我在intellij中的spring initializer中选择版本比较高的原因，总是在build的时候出来一些warning或者是例如 spring-boot-maven-plugin无法在aliyun的repo中找到的问题，所以我改为使用STS了，感觉没必要在工具上花太多的时间。

STS创建的工程和Intellij创建的工程是一样的，因为两者都是maven的工程，但是我目前还没搞明白，maven wrapper是干啥的@todo

看一下创建出来的工程pom.xml文件的内容吧：

```xml
    <parent>
    <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>io.github.libai8723.springboot</groupId>
    <artifactId>chapter-03-01</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>chapter-03-01</name>
    <description>Demo project for Spring Boot</description>
```

这个和之前学习maven的时候差不多，主要是parent这个元素在spring boot中起到了重要的作用，把该设置的东西基本都设置好了。@todo，所以我之前学习maven的课程要赶紧完结了，因为还没学习多module的maven工程，以及带有parent元素的工程的原理。

那么我们来看看其他的依赖吧：

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

从依赖关系来看，也就是我们在Spring Initilizer中选择的组建，spring web，dev tools，还有一个内置的数据库h2。 可能因为工程认为测试非常重要，所以默认增加spring-boot-starter-test的依赖，但是排除掉org.junit.vintage的依赖了，这也是maven的用法，而且这里面的依赖中都没有指定version的信息，@todo，看看是不是在parent里面都指定了呢？

我们再看看build的元素

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

之前我以为build没有什么说道的，但是后来仔细研究了一下发现Fat Jar还是非常有讲究的，为了能把Jar文件引入Jar文件作为依赖，还是需要自己实现class loader的，所以这里的spring-boot-maven-plguin就是干这个事情的，非常有用。因为按照Oracle的Jar的规范，nested jar是不被支持的，只能自己实现class loader的这些机制。@todo，所以看到这里的话，其实还需要了解一下maven的插件是如何编写的。

@todo，之前的链接找不到了，有时间的话，先看这个链接吧：[ref to spring boot maven plugin](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/maven-plugin/reference/html/)

下面我们看一下代码吧。但是在看代码之前，我们还是应该简单说明一下，Java Annotation的意义，其实在Oralce的官方文档中是有针对Java Annotation的专门的讲解的，但是我却从没有完成过阅读，可能比较难吧。@todo

[Java Annotation](https://docs.oracle.com/javase/tutorial/java/annotations/)

但是有时间的话，大家都应该学习一下基本的注解的知识，因为注解配合上反射，能做出来非常多非常强大的东西，我说的就是框架。因为有非常多框架是利用反射的机制来扫描classpath下面的类，分析类中的注解来完成类的实例化和初始化的，这个非常重要。

那么我们看一下代码吧：

```java
package io.github.libai8723.springboot.chapter0301;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@SpringBootApplication
public class Chapter0301Application {
    @RequestMapping("/")
    public String home() {
        return "Hello, World! and the rest of the world";
    }

    public static void main(String[] args) {
        SpringApplication.run(Chapter0301Application.class, args);
    }
}
```

和原书的有一些区别，原书的代码是 @EnableAutoConfiguration的注解，而使用Spring Boot Initializer生成的代码默认使用的是@SpringBootApplication的注解，那么我们看一下区别是什么：

从@SpringBootApplication的java doc来看，它是包含了下面的若干个注解：

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters={@Filter(type=CUSTOM, classes={TypeExcludeFilter.class}), @Filter(type=CUSTOM, classes={AutoConfigurationExcludeFilter.class})})
@Target(value={TYPE})
@Retention(value=RUNTIME)
@Documented
@Inherited
```

其中就包含了@EnableAutoConfiguration，这其实就是在前面的博客中提到的自定义注解的用法。

除此之外还有一个@RestController的注解，它是下面的注解的集合

```java
@Target(value={TYPE})
@Retention(value=RUNTIME)
@Documented
@Controller
@ResponseBody
```

其中 @Controller 又包含了 @Component 注解，按照Controller的javadoc的解释来看，这个注解表明自己是一个是作为一个Component的特殊形式对外提供服务的，因为是一个特殊形式的Component，所以可以被autodetect机制发现，并被初始化为bean，通常和被@RequestMapping标记的handler methods一起使用。

这里@RestController还包含了@ResponseBody的注解，这个注解的含义是说，一个handler method的返回值应该被bind to 对应的web response body，于是就不需要按照返回值去找对应的view了，从4.0版本开始，这个注解可以被增加到Type级别，这种情况下，所有的类方法都继承了这个特性。

@todo
