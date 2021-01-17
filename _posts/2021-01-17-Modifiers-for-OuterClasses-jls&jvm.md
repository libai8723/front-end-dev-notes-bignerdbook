---
title: "The Modifiers for Outer Classes" 
date: 2021-01-17

tag: "JLS&JVM Spec"
---

关于外部类（Outer Class）的Access Modifiers的要求。只有public, abstract & final 是允许的。

<!--more-->

## 一、外部类的Access Modifiers（访问控制符号）

主要是在看Inner Class的这个章节的时候[Nested Classes](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html)产生的疑问，因为内部类的时候，提到了外部类只能使用 outer classes can only be declared public or package private.

所以我google了一下，看到了一篇文章，详细的讲了一下关于外部类的访问控制符号：[Class Modifiers Can Not be private or protected](https://www.programmingmitra.com/2016/10/why-a-java-class-can-not-be-private-or-protected.html)

就详细的看一下吧：

## 二、编译器的提示

当我们试图把private 和 protected 关键字用在一个外部类上面的时候，编译器会提示 **“Illegal modifier for the class your_class_name; only public, abstract & final are permitted”**

谈到对于一个class可以使用什么样子的修饰符的时候，我们还是先看一下有哪些修饰符吧：

1. **private** 可以用来修饰（字段，类，方法，接口），使用private修饰的东西只能在它被定义的东西内访问。
2. **default** 在同一个package中可以被访问，通常也被称为是package-private
3. **protected** 在同一个包中可以访问，但是对于包外面的这个类的子类是可以访问的。
4. **public** 就是在所有的地方都可以访问了，比较开放。

## 三、为什么一个Outer Class不能被设定为private

其实对于一个field被设定为private来说，是比较好理解的，就是只能从这个field所在的类内部来访问这个属性。要想访问这个属性其实还是有点难度的，比如下面的例子就给了一种通过instance var来访问私有属性的例子，但是这个例子看起来非常的不科学，感觉这么写毫无必要。

```java
package com.example;
class A {
    private int a = 10;

    // We can access a private field by creating object of same class inside the same class
    // But realy no body creates object of a class inside the same class
    public void usePrivateField(){
        A objA =  new A();
        System.out.println(objA.a);
    }
}
```

从上面的例子可以看出来，是非常的没有必要的。

现在让我们再会议一下上面的关于private的定义，就是在被修饰的对象的定义的空间内被访问，对于一个outer class来说，它的外部就是一个package；所以建设我们可以把private用来修饰一个class，那么意思就是仅仅在这个package里面的其他类可以访问这个类，在这个package之外的类不能访问这个类；但是这个作用，和default关键字也就是什么修饰符都没有的效果是一样的，所以增加一个private的访问修饰符仅仅是让大家在理解上更有歧义，所以不允许这么干。

## 四、为什么一个Outer Class不能被设定为protected

首先我们来看一下protected关键字在应用到一个field上的作用：

被protected修饰的field在同一个包里面是可以访问的。但是在其他包里面的话，则必须是在field所在的类的子类中来访问。**这个在理解上其实是有点绕的。**


下面的例子还是非常好的：

```java
package com.example;
public class A {
    protected int a = 10;
}
```

```java
package com.experiment;
public class B extends A {

    // Outside of the package protected field can be accessed through inheritance
    public void printUsingInheritance() {
        System.out.println(a);
    }

    // In child class we can access protected field through instantiation of child class
    // But should we do that ? .... No
    public void printUsingInstantiation() {
        B b = new B();
        System.out.println(b.a);
        System.out.println(this.a);


        // But not through instantiation of the class which contains the protected field
        A a = new A();
        System.out.println(a.a); // Compilation error “The field A.a is not visible”
    }
}
```

首先我们看到了，在上面中，B类在另外一个包中，它继承了A，所以在这个类中是可以访问a这个属性的。  System.out.println(b.a); 这一句话非常绕，其实就是和上面那一段代码类似，非要在一个类中，生成一个类的实例，然后通过这个实例来访问这个实例的私有变量一样。这个也是一样的，在A的子类中，通过子类的实例来访问继承的父类的protected的属性，是允许的，但是不建议这么干，语义上非常绕，没有什么意思。

然后System.out.println(this.a); 这个是没啥问题的，作为B子类继承了A类，可能要扩展一些逻辑，利用A的被保护的属性，无可厚非，应该是可以这么干的。

但是下面的在B里面声明了一个A的对象实例，然后访问对象实例的被保护的字段是不可以的，属于编译错误。

现在我们理解上面的意思之后，就会发现使用protected修饰class非常的荒谬。

假设我们可以使用protected来修饰class的话，那我们在这个class的包中是可以访问这个类的，这和default修饰关键字就冲突了，有歧义。假设我们想从另外一个包里面来访问被protected修饰的class，那我们就需要extends这个class所在的包，而这个包是没有办法被extends的，所以这就是出现了荒谬的情况。

所以总结如下：

## 对于outer class来说，要么被public修饰，要么没有任何修饰符号（AKA package private）

但是还有其他可以修饰的，比如说abstract还有final。但是这俩应该是对于可见性没有影响的，前者是定义一个抽象类.

后者final class是无法被继承来生成一个子类的，例如java.lang.String类，当然final也可以用来修饰一个method，这个方法就不能被override了。

还要说一下static关键字也不能用来修饰outer class，是编译的错误。
