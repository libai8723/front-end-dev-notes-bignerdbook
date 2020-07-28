---
title: "微服务架构实战-第三章 第三节面向切面（AOP）" 
date: 2020-07-28
tag: "微服务架构实战"
excerpt_separator: <!--more-->
---

第三章第三小节主要讲述AOP，从spring in action 4th版本我们知道 Spring自己实现的AOP是基于proxy的设计模式实现的，但是基本足够了。更强大的可以看AspectJ的实现方案。
<!--more-->

## 一、面向切面（AOP）中的一些名词

（2020-07-28 21：17：00）今晚在看这个AOP的时候，又回头阅读了一下SIA 4th版本，发现之前自己没有看懂阿，这次好像看懂了。那么把SIA 4th版本中的比喻放在这里吧

1. 通知（Advice）：感觉这个翻译不对，不采用这本书的翻译了。Advice是Aspect的purpose，Aspect有注定应该他们做的工作，这个工作被成为advice。Advice同时定义了**What** 和 **When**，也就是在什么时间，和要做什么事情。这里的事情更多是一个函数，这里的时间更多的说的是（Before，After，After-returning，After-throwing，Around）
2. 连接点（JointPoint）：SIA 4th中的例子是说，你的程序中可能有成千上万个可以被advice使用的机会。这些机会点就被人们认知为JointPoint，一个JointPoint是一个程序中可以被Aspect插入（plugged in）的一个点。
3. 切入点（Pointcut）：PointCut可以用来减少潜在的JointPoint的数量，来被一个Apsect进行advised。更加形象的说，如果Advice定了what和where，那么PointCut就定义了**Where**
4. 切面（Aspect）： 一个切面就是Advice和pointcut的结合，也就是说， **When** **Where** **What**，都齐备了，可以开始干活了。
5. 引入（Introduction）: 一个introduction允许我们针对一个现存的类添加新的方法和属性。这个真的好强阿，例子是说，我们可以新增一个Auditable的advice，用来跟踪一个对象最后被修改的时间。我们可以简单的用一个setLastUpdated(Date d)的函数和一个instance var来保存这个状态，这个新的方法和instance var可以被引入现存的类，而无需去改变这些类。
6. 目标对象（Target）:这个暂时没讲到 @todo
7. AOP代理：AOP Proxy：暂时没看到 @todo
8. 织入（Weaving）：织入这个翻译非常形象，就是我们的advice，在指定的PointCut上怎么和被advised的object怎么发生了关系。

## 二、不同种类的切面

### 前置通知

### 后置返回通知

### 后置异常通知

### 后置最终通知

### 环绕通知

## 三、代码工程

### POM文件

![pom file for chapter-03-03](/front-end-dev-notes-bignerdbook/assets/img/chapter-0303-pom.png)

上图是我在按照书中的代码进行工程创建的时候，STS的一个提示，看到划线的部分有一些一场。提示的具体的信息是：“Overriding Managed version 2.13.3 for log4j-web”

从这个提示我大概明白了，按照之前阅读的maven的教程，我们依赖一个artifact的时候要指明我们依赖的版本。但是spring boot为了简化这个事情，直接把依赖的东西自己管起来了，所以叫做managed ersion。终于明白这个managed是什么含义了，大致意思就是良好管理的，甚至是被框架所托管的。

并且从作者的书中可以看出不喜欢spring boot默认的logback的日志库，一定要环城log4j，不明白好在哪里。但是作者喜欢就行了，不管这么多了。


