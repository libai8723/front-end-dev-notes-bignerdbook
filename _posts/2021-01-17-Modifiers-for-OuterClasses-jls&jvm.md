---
title: "The Modifiers for Outer Classes" 
date: 2021-01-17

tag: "JLS&JVM Spec"
---

关于外部类（Outer Class）的Access Modifiers的要求。只有public, abstract & final 是允许的。

<!--more-->


## 外部类的Access Modifiers（访问控制符号）

主要是在看Inner Class的这个章节的时候[Nested Classes](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html)产生的疑问，因为内部类的时候，提到了外部类只能使用 outer classes can only be declared public or package private.

所以我google了一下，看到了一篇文章，详细的讲了一下关于外部类的访问控制符号：[Class Modifiers Can Not be private or protected](https://www.programmingmitra.com/2016/10/why-a-java-class-can-not-be-private-or-protected.html)

就详细的看一下吧：


## 编译器的提示

当我们试图把private 和 protected 关键字用在一个外部类上面的时候，编译器会提示“Illegal modifier for the class your_class_name; only public, abstract & final are permitted”

谈到对于一个class可以使用什么样子的修饰符的时候，我们还是先看一下有哪些修饰符吧：

1. **private** 可以用来修饰（字段，类，方法，接口），使用private修饰的东西只能在它被定义的东西内访问。
2. **default** 在同一个package中可以被访问，通常也被称为是package-private
3. **protected** 在同一个包中可以访问，但是对于包外面的这个类的子类是可以访问的。
4. **public** 就是在所有的地方都可以访问了，比较开放。
