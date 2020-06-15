---
title: "A quick introduction to DDD" 
date: 2020-06-15
tag: Domain-Driven Design
---

## DDD
为什么又开始看DDD了呢，是因为最近有一个DDD的培训。领导说你都看过书了，就不用去了，但是仔细回想一下，基本仅仅是把DDD的书看完了Part 1，而且感觉似懂非懂，所以赶紧看一下。

在刷手机的时候看到了一篇关于DDD的简介的文章还是不错的：

[An Introduction to DDD](http://www.methodsandtools.com/archive/archive.php?id=97)

为了让记忆更加深刻一点，还是简单写一写笔记和日志吧。

The philosophy of domain-driven design (DDD) - first described by Eric Evans in his book of the same name - is about placing our attention at the heart of the application, focusing on the complexity that is intrinsic to the business domain itself. We also distinguish the core domain (unique to the business) from the supporting sub-domains (typically generic in nature, such as money or time), and place appropriately more of our design efforts on the core.

DDD的哲学是把我们的最大的注意力放在应用的核心上，关注业务领域本身的固有的（intrinsic）复杂性。我们要能区分核心的领域和支撑性的子领域的区别。支撑性的子领域通常都是一些通用的领域，例如时间与金钱的领域。而我们要做的事情是把尽可能多一些的时间和精力放在core domain上。

## 关于代码和模型 Of Code and Models

With DDD we're looking to create models of a problem domain. The persistence, user interfaces and messaging stuff can come later, it's the domain that needs to be understood, because that's the bit in the system being built that distinguishes your company's business from your competitors. (And if that isn't true, then consider buying a packaged product instead).

使用DDD的方法，我们试图寻找了一个问题领域的模型。而关于技术开发中的持久化，UI，消息传递机制，我们后面再谈。因为核心的问题是理解领域的知识。那才是我们的系统有别于其他人系统的核心点。

By model we don't mean a diagram or set of diagrams; sure, diagrams are useful but they aren't the model, just different views of the model (see Figure). No, the model is the set of concepts that we select to be implemented in software, represented in code and any other software artifact used to construct the delivered system. In other words, the code is the model. Text editors provide one way to work with this model, though modern tools provide plenty of other visualizations too

说到模型，我们不是说一个或者几个示意图就是一个模型。当然，示意图非常的有用，但是它们本身并不是模型，仅仅是关于model的不同的view，作者给了一个例子很生动：

![Model vs Views of the Model](http://www.methodsandtools.com/archive/ddd1.gif)

模型是我们选择出来的用来在软件中实现的一系列的概念，我们使用代码或者其他的artifact来构建这个需要被交付的系统。换句话说。code就是模型。

This then is the first of the DDD patterns: a model-driven design. It means being able to map - ideally quite literally - the concepts in the model to those of the design/code. A change in the model implies a change to the code; changing the code means the model has changed. DDD doesn't mandate that you model the domain using object-orientation - we could build models using a rules engine, for example - but given that the dominant enterprise programming languages are OO based, most models will be OO in nature. After all, OO is based on a modelling paradigm. The concepts of the model will be represented as classes and interfaces, the responsibilities as class members.

然后我们就讲到了DDD中的第一个设计模式：model-driven design，就是字面的意思。这个设计模式本身的意思就是模型和code是相互mapping的关系。如果模型发生了变化，那么code也要变化。

## Speaking the Language

Let's now look at another bedrock principle of domain-driven design. To recap: we want to build a domain model that captures the problem domain of the system being built, and we're going to express that understanding in code / software artifacts. To help us do that, DDD advocates that the domain experts and developers consciously communicate using the concepts within the model. So the domain experts don't describe a new user story in terms of a field on a screen or a menu item, they talk about the underlying property or behaviour that's required on a domain object. Similarly the developers don't talk about new instance variables of a class or columns in a database table.

然后我们提到了DDD中的基石的原理，所谓的ubiquitous language。 我们希望构建出一个领域模型，模型可以抓住问题领域中的主要的问题，并且我们将会使用代码或者软件制品来表达我们对问题的理解。为了帮助我们做到这一点，DDD拥护一种行为，让领域专家和开发人员，有意识的使用模型中的概念来进行交流。

以至于我们的领域专家不需要使用“屏幕上的一个字段”或者“目录中的一个选项”来描述一个新的User Story，让领域专家来谈对于一个领域对象来说，它应该具有什么样的属性和行为。

同样的，我们的开发人员也不会说，我们要创建一个新的类的实例或者是在数据表中新增一列。

Doing this rigorously we get to develop a ubiquitous language. If an idea can't easily be expressed then it indicates a concept that's missing from the domain model and the team work together to figure out what that missing concept is. Once this has been established then the new field on the screen or column in the database table follows on from that.

通过严格的遵守这样一种沟通的模式，我们将会开发出一种ubiquitous language。如果一个idea不能被简单的表达，那说明我们的领域模型中仍然缺少一些概念，我们需要团队协作来找出缺少的概念。并最终实现这个概念。