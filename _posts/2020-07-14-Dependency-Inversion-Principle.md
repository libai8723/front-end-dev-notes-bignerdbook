---
title: "Dependency Inversion Principle" 
date: 2020-07-14
tag: Dependency Inversion Principle
---

依赖反转原理是OOD中一个非常出名的原理（Principle），之前一直和DI（依赖注入）搞混了，本次特别学习了一下

## Dependency Inversion Principle

之前看看到DDD Introduction的时候看到一篇关于Dependency Inversion的博客还是写的很好的，因此快速阅读一下

[Dependency Inversion Principle](https://wikimili.com/en/Dependency_inversion_principle)

In object-oriented design, the dependency inversion principle is a specific form of decoupling software modules. When following this principle, the conventional dependency relationships established from high-level, policy-setting modules to low-level, dependency modules are reversed, thus rendering high-level modules independent of the low-level module implementation details. The principle states: [1]

 A. High-level modules should not depend on low-level modules. Both should depend on abstractions (e.g. interfaces).

 B. Abstractions should not depend on details. Details (concrete implementations) should depend on abstractions.

在OOD中，依赖反转的原理是一种解耦软件模块的特定的方式。当我们遵从这个原理的时候，那些传统的从high-level——策略设定的模块 到 low-level——依赖模块的的关系将被反转，因此将会使得高层级的模块独立于底层级的模块的具体实现，这个原理详细是这么说的：

 1. 高层级的模块不应该依赖于底层及的模块。它们俩都应该依赖于抽象（例如：interfaces）
 2. 抽象不应该依赖于细节。细节（Concrete implementations）应该依赖于抽象。

By dictating that both high-level and low-level objects must depend on the same abstraction, this design principle inverts the way some people may think about object-oriented programming. [2]

当这个原理说到无论是高层级还是底层及的对象都已经依赖同一个抽象，那么这个原理就反转了人们通常对于OOP的理解了。

The idea behind points A and B of this principle is that when designing the interaction between a high-level module and a low-level one, the interaction should be thought of as an abstract interaction between them. This not only has implications on the design of the high-level module, but also on the low-level one: the low-level one should be designed with the interaction in mind and it may be necessary to change its usage interface.

DI原理要求的A点和B点背后所隐含的意思是，当我们设计一个高层级模块和一个低层级模块之间的交互的时候，两个模块之间的交互应该被考虑成一个在它们两者之间的抽象。这一点不仅仅会影响高层级模块的设计，也会影响到低层级模块的设计，低层级模块在设计的时候应该考虑与这个抽象的交互，当有必要的时候应该变更它的interface。

In many cases, thinking about the interaction in itself as an abstract concept allows the coupling of the components to be reduced without introducing additional coding patterns, allowing only a lighter and less implementation dependent interaction schema.

在很多情况下，把两个模块之间的交互考虑成一个抽象的概念，可以允许我们减少耦合在一起的组件，而不额外的引入额外的编程模式，允许更轻量的并且对实现有较小依赖的交互模式的实现。

When the discovered abstract interaction schema(s) between two modules is/are generic and generalization makes sense, this design principle also leads to the following dependency inversion coding pattern.

并且当我们发现了一个位于2个模块之间的抽象的交互模式是通用且可以泛化，并且泛化是有意义的时候，那么这个这里原理就能推导出下面的依赖反转的编码模式。

### Traditional layers pattern （传统的分层模式）

In conventional application architecture, lower-level components (e.g. Utility Layer) are designed to be consumed by higher-level components (e.g. Policy Layer) which enable increasingly complex systems to be built. In this composition, higher-level components depend directly upon lower-level components to achieve some task. This dependency upon lower-level components limits the reuse opportunities of the higher-level components. [1]

在大家约定俗称的应用架构中，低层级的组件被设计用来被高层级的组件来消费，这样促进了构建系统的复杂性。在这种组合模式中，高层级的组件直接依赖于低层级的组件来实现某些任务。对于低层级组件的依赖，限制了高层级组件的重用的可能性。

![Traditional Layers Pattern](https://upload.wikimedia.org/wikipedia/commons/4/42/Traditional_Layers_Pattern.png)

Traditional Layers Pattern

The goal of the dependency inversion pattern is to avoid this highly coupled distribution with the mediation of an abstract layer, and to increase the re-usability of higher/policy layers.

DI Pattern的目标就是使用中间的抽象层来避免这种高耦合的现象，并且增加高层级/策略层的重用可能性。

### Dependency inversion pattern

With the addition of an abstract layer, both high- and lower-level layers reduce the traditional dependencies from top to bottom. Nevertheless, the "inversion" concept does not mean that lower-level layers depend on higher-level layers. Both layers should depend on abstractions that draw the behavior needed by higher-level layers.

因为有了额外的一层抽象层，所以现在高层级和低层级减少了传统的同上到下的依赖。不过，inversion这个概念并不是意味着低层级反过来依赖于高层级了。而是两个层同时都依赖于同一个抽象层，这个抽象层给出了高层级所要求的行为模式。

![DI Pattern](https://upload.wikimedia.org/wikipedia/commons/8/8d/DIPLayersPattern.png)

DI Pattern

In a direct application of dependency inversion, the abstracts are owned by the upper/policy layers. This architecture groups the higher/policy components and the abstractions that define lower services together in the same package. The lower-level layers are created by inheritance/implementation of these abstract classes or interfaces. [1]

在直接的应用依赖反转中，这个抽象层是被上层或者策略层所拥有的。这种架构组中，高层级/策略层中的组件和这个抽象层定义了低层级服务应该提供的服务，在同一个package中。低层级被创建出来，使用集成/实现的方式来实现或者完善对应的接口或者抽象类。

The inversion of the dependencies and ownership encourages the re-usability of the higher/policy layers. Upper layers could use other implementations of the lower services. When the lower-level layer components are closed or when the application requires the reuse of existing services, it is common that an Adapter mediates between the services and the abstractions.

这种依赖的反转的模式，和对于抽象层的所有权是鼓励我们对于高层级/策略层的重用的。高层级使用低层级的具体实现。当低层级的组件不可以使用的时候，或者当我们的应用需要使用一些已经存在的服务的时候，对于我们来说就可以加入一个Adapter中间层，在对应的现存服务和我们的抽象层之间。

### Dependency inversion pattern generalization

依赖反转模式的泛化。

In many projects the dependency inversion principle and pattern are considered as a single concept that should be generalized, i.e., applied to all interfaces between software modules. There are at least two reasons for that:

在很多项目中依赖反转原理或者依赖反转模式被认为是一个应该被泛化的概念，例如在所有的modules之间都使用interface。这样做至少有2个原因：

1. It is simpler to see a good thinking principle as a coding pattern. Once an abstract class or an interface has been coded, the programmer may say: "I have done the job of abstraction".

2. Because many unit testing tools rely on inheritance to accomplish mocking, the usage of generic interfaces between classes (not only between modules when it makes sense to use generality) became the rule.

* 把一个思考的原理（Dependency Inversion Principle）看作是一个编码的模式更简单。我们的编程人员一旦完成了一个抽象类或者interface，那么他可以说我已经完成了抽象的工作了。

* 因为很多单元测试工具都依赖于继承来完成mocking的功能，在类之间广泛的使用interfaces于是成为了规则。简单理解就是为了使用单元测试工具从而建立的规则。

If the mocking tool used relies only on inheritance, it may become necessary to widely apply the dependency inversion pattern. This has major drawbacks:

如果我们的mocking工具只能依赖于继承，那么我们确实有必要广泛的使用依赖反转模式，但是这样会有一些明显的缺点：

1. Merely implementing an interface over a class isn't sufficient to reduce coupling; only thinking about the potential abstraction of interactions can lead to a less coupled design.
2. Implementing generic interfaces everywhere in a project makes it harder to understand and maintain. At each step the reader will ask themself what are the other implementations of this interface and the response is generally: only mocks.
3. The interface generalization requires more plumbing code, in particular factories that generally rely on a dependency-injection framework.
4. Interface generalization also restricts the usage of the programming language.

* 针对一个类实现了一个差强人意的interface并不足以减少耦合；只有认真的思考在模块之间的交互的抽象行为才能减少耦合性很高的设计。
* 在一个项目中充满了泛化的接口之后，对于后续的读者和维护者来说都是很难阅读和理解的。我们每读一步，总是会问到针对这个interface还有其他的实现吗，通常的回答就是仅仅有mock类实现了它。
* 泛化的使用interface的后果会导致更多的接水管的代码，尤其是使用DI框架的工厂类。
* interface泛化也会限制对于编程语言的使用。

The presence of interfaces to accomplish the Dependency Inversion Pattern (DIP) has other design implications in an object-oriented program:

通过使用interface的方式来实现依赖反转的模式，对于OOP来说还有其他设计上的影响：

1. All member variables in a class must be interfaces or abstracts.
2. All concrete class packages must connect only through interface or abstract class packages.
3. No class should derive from a concrete class.
4. No method should override an implemented method. [1]
5. All variable instantiation requires the implementation of a creational pattern such as the factory method or the factory pattern, or the use of a dependency-injection framework.

* 所有的类成员变量必须是interface或者抽象的
* 所有的实现类的包只能通过interface或者抽象的类包来连接到其他类和包
* 不可以从实现类派生出其他的类
* 不应该override一个已经实现的method
* 所有的对象变量的出实现都需要一个生成模式的实现，例如工厂方法，或者使用一个依赖注入框架

@todo，剩下还有大概60%的内容没看，暂时不看
