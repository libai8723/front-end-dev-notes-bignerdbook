---
title: "微服务架构实战-第三章 第三节面向切面（AOP）" 
date: 2020-07-28
tag: "微服务架构实战"
excerpt_separator: <!--more-->
---

第三章第三小节主要讲述AOP，从spring in action 4th版本我们知道 Spring自己实现的AOP是基于proxy的设计模式实现的，但是基本足够了。更强大的可以看AspectJ的实现方案。
<!--more-->

## 一、面向切面（AOP）中的一些名词

1. 切面（Aspect）： @todo
2. 连接点（JointPoint）：@todo
3. 通知（Advice）：@todo
4. 切入点（Pointcut）：@todo
5. 引入（Introduction）:@todo，这个名词在 SIA 4th里面没有看到
6. 目标对象（Target）
7. AOP代理：AOP Proxy
8. 织入（Weaving）：

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

并且从作者的书中可以看出不喜欢spring boot默认的logback的日志库，一定要环城log4j，不明白好在哪里。