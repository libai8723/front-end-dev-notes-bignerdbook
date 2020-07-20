---
title: "微服务架构实战" 
date: 2020-07-20
tag: MicroService in Action, Spring Boot
---

## 第三章 微服务之Spring Boot

为什么从第三章开始写呢，因为去桂林的时候在火车上快速的翻书翻了一遍，对应的第一章完全是综述和介绍性的章节，第二章完全是关于设计原则（Design Principle）级别的东西，所以这个东西对不齐下个月的汇报和总结阿，所以我们要快速的看完这本书。

其实在Spring in Action的书籍中，我们已经知道了Spring的一些不太让人开心的事情，那就是大量的xml配置文件的工作，但是从Spring in Action 4th edition 到 5th edition来看，并不是所有的xml配置文件都可以被取消，因为有些时候为了完成AOP的设置，而且是在第三方类库上完成AOP的配置的。所以总结来说，大量的配置工作可以被取代，但是有些无法取代的东西，还是在那边，所以看一下4th的版本，有一个大概的发展历程的了解是非常有好处的。

原书45页开始使用STS来进行第一个案例的教学，因为公司使用的是Intellij的IDE,所以咱们开始使用Intellij的IDE吧，

![创建一个新的SpringBoot的应用，使用Intellij](/front-end-dev-notes-bignerdbook/assets/img/CreateAnNewSpringBootProject.png)

