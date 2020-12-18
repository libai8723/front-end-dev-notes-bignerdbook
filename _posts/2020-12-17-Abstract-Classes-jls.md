---
title: "The Abstract Classes" 
date: 2020-12-17

tag: "JLS&JVM Spec"
---


## 抽象类

在阅读ASM JAVA的时候，因为提到了ClassVisitor是一个抽象类，所以这里特别看了一下什么是抽象类。

按照Oracle Java的定义，抽象类是一个类被声明为abstract，但是抽象类本身可以包含抽象方法或者不包含抽象方法。

抽象类是不可以被实例化的，但是可以被字类所继承。

那么什么是抽象方法呢，就是用abstract来修饰的方法声明，但是这个方法是没有方法体的，也就是没有实现。

## 抽象类的子类

当一个抽象类被子类继承的时候，通常来说子类会实现所有父类的抽象方法，但是这个不是必要的要求。当子类没有实现所有的抽象方法的时候，那么它本身也必须是一个抽象类。

```text
注意：在一个interface中的方法，如果没有被声明为default方法

或者是static方法的话，那么它就是一个隐式的抽象方法。通常来说

abstract这个关键字不会和interface method一起使用

（虽然可以一起用，但是不是必须的。）
```

## 抽象类与Interface的对比

抽象类和interface非常的相似，它们都不可以被实例化，它们可能都包含有方法体和无方法体的methods的混合体。

但是对于抽象类来说，你可以声明不是static final修饰的field

使用抽象类，你也可以定义public，protected，或者是private 的方法体。

但是当你使用interface的时候，所有的field都自动是public static final 修饰的field了，而且所有声明或者定义（也就是有方法体）的方法都是public的。

另外你仅仅可以扩展一个class，无论是不是abstract类型的class，但是你却可以implement多个接口。

## 如何选择interface 还是 abstract class

如果满足下面的几个条件，那么就选择abstract class

1. 需要在若干个彼此紧密关联的类之间共享代码
2. 你期望扩展你的抽象类的子类有很多公共的方法和field，或者是你需要使用public意外的access modifier
3. 你需要使用non-static或者是non-final类型的field，这样的情况下允许你定义一些方法来访问和修改对应的对象的状态

如果满足下面几个条件，那么就选择interface：

1. 你期望很多个彼此不相关的类来实现你的接口。举个例子Comparable和Cloneable这两个接口，很多类都实现了，这些类往往彼此都是没有什么关系的。
2. 你希望规定一个特定数据类型的行为，但是如此同时你不关心行为是怎么实现的。
3. 想利用多种type的继承

## 当一个抽象类实现了一个interface

在[Interfaces](https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html) 章节中我们知道一个class要implement一个interface的话，就要实现interface中的所有的method，但是如果是一个抽象类的话，那就不必实现所有的方法。但是extends这个抽象类的concreate class就必须实现所有的方法了。

## 抽象类中的成员

一个抽象类可以有静态field和静态method，调用静态方法的时候，直接用class reference就可以了。
