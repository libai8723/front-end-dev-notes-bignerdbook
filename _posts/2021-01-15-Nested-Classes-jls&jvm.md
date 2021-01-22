---
title: "The Nested Classes" 
date: 2021-01-15

tag: "JLS&JVM Spec"
---



## 一、内嵌类 Nested Class

其实目前我并不知道具体的内嵌类的好处，感觉无非就是代码上的整洁，对外封装不可见。写这个帖子的主要的目的是在阅读java asm的时候，看到了Inner Classes的class级别的Attribute。所以就试验了一下

代码其实非常简单：

```java
public class Person{
    public void foo(){
        class Job{
            String type = "boring";
        }
    }

    public void bar(){
        class Job {
            String type = "freeStyle";
        }

        class HuaweiJob{
            String level = "hell";
        }
    }
}
```

这个编译出来是没有什么错误的。因为两个Job是在不同的Method中声明的，所以并部署冲突，可以看到编译出来的类如下

```bash
libai@libai-HP-EliteBook-820-G2:~$ ls *.class
'Person$1HuaweiJob.class'  'Person$1Job.class'  'Person$2Job.class'   Person.class
```

所以很明确了是编译出来2个不同的Job的类了。

那么我们继续看一下javap的结果吧

```bash
~$ javap -verbose ClassFileName > ClassFileNameP.txt
```

下面是Person.class反编译出来的部分结果:

```text
SourceFile: "Person.java"
NestMembers:
  Person$1HuaweiJob
  Person$2Job
  Person$1Job
InnerClasses:
  #5= #4;                                 // HuaweiJob=class Person$1HuaweiJob
  #8= #7;                                 // Job=class Person$2Job
  #8= #9;                                 // Job=class Person$1Job
```

上面是Person.class反汇编的最后几行属性，就是class file的Attributes了，可以看到主要是表明了有NestMembers，还有InnerClasses

其实不太明白这两者有什么区别，需要进一步阅读@todo

然后我们在看其中一个Job类的反编译的结果

```text
SourceFile: "Person.java"
EnclosingMethod: #18.#19                // Person.foo
NestHost: class Person
InnerClasses:
  #26= #5;                                // Job=class Person$1Job
```

首先可以看出来对应的属性说明自己是包含在一个Method中声明的，这个方法是Person.foo。这里也有InnerClasses的属性，不太懂是干啥用的。@todo

也就是他们俩相互指向的意思了。

但是等看到这个Job的默认构造函数的时候，对应的指令的时候，没有明白是什么意思：

```text
  Person$1Job(Person);
    descriptor: (LPerson;)V
    flags: (0x0000)
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0
         1: aload_1
         2: putfield      #1                  // Field this$0:LPerson;
         5: aload_0
         6: invokespecial #2                  // Method java/lang/Object."<init>":()V
         9: aload_0
        10: ldc           #3                  // String boring
        12: putfield      #4                  // Field type:Ljava/lang/String;
        15: return
      LineNumberTable:
        line 3: 0
        line 4: 9
```

上面的应该对应的是Person.foo中的Job类型的默认构造函数的反编译的指令，这里面的函数的descriptor为啥有一个LPerson;的参数呢？

这个就需要好好再深入研究了。


## 二、Nested Class在Oracle文档网站上的阅读

其实关于Nested Class的[说明](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html)，在Oracle的官方文档里面说的是非常清晰的。

第一句就表明了这个事情，jls是允许我们在一个类中定义另外一个类的，但是在术语上我们区分两类内嵌类

1. 静态内嵌类（static nested class）： 被static修饰的内部的class的定义。
2. 内部类（None-Static nested class AKA. inner classes)：通常意义上没有被static关键字修饰的内嵌类

```java
class OuterClass {
    ...
    static class StaticNestedClass {
        ...
    }
    class InnerClass {
        ...
    }
}
```

一个内嵌的类是包含它的类的成员。对于一个 非静态的内嵌类（Inner Classes）对于包含它的类的其他的成员都是可以访问的，即使这些成员被声明为private也是可以的。

但是 静态内嵌类 不能访问包含它的类的其他成员。

作为OuterClass的一个成员，内嵌类可以被声明为private， public， protected或者 package private（AKA default 无访问修饰符），但是对于一个Outer Classes来说只能被声明为public或者是package private。

### 2.1 内嵌类的好处

1. 内嵌类是一种组织在一个地方使用的类的逻辑上的方法：如果一个类只对另外一个类有用的话，那么在逻辑上最好把这个类内嵌在需要它的类内部，让它们俩带在一起。例如把 helper类型的类内嵌到它帮助的类内部，有助于让整个包更简洁。

2. 增加封装的程度：考虑我们有两个顶层的类，A与B，B需要访问A中被声明为private的成员，如果没有内嵌类的方式的话，那就只能把B需要访问的A的成员声明为public或者package private，但是有了内嵌类之后，我们就可以把B隐藏在A中，这样A的成员依然可以声明为private，但是B可以访问到它们，并且B本身也可以与外部世界隔开。

3. 内嵌类可以牵引出更具有可读性和更具备维护性的代码：我们把一些小型的类内嵌到top-level类中，从而让代码更靠近使用到它的地方

### 2.2 Static Nested Classes 和 Inner Classes

上面提到了Nested主要是分为静态内嵌类和内部类，先看一下静态内嵌类

#### 2.2.1 静态内嵌类

就像class method 和 class variable一样，静态内嵌类是和它的外部类关联的（associated），下面这句话比较精确了，就像一个静态类方法一样，一个静态内嵌类不能直接引用实例的变量（instance variables)或者是其他的非静态方法：静态内嵌类只能通过对象引用（object reference to Class instance）来访问它们。

```text
Note: 一个静态内嵌类与包含它的外部类的实例成员的交互方式就像其他顶层类一样。实际上，一个静态内嵌类在行为上来看就和一个顶层类是一样的，内嵌到另外一个顶层类中仅仅是为了打包的方便。
```

静态内嵌类在使用上，需要连着它的外部类一起使用，采用下面的方式

```java
OuterClass.StaticNestedClass nestedObject =
     new OuterClass.StaticNestedClass();
```

#### 2.2.2 Inner Classes

就像实例方法和实例变量那样，一个内部类是与包含它的外部类的实例相关联的（associated）并且可以直接访问相关联的实例对象的方法和字段。另外因为一个内部库是与一个实例相关联的，那么它不可以定义任何静态成员。

下面这句话很重点：

一个内部类的的实例对象存在于其外部类的实例中。下面是代码的示例：

```java
class OuterClass {
    ...
    class InnerClass {
        ...
    }
}
...
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```

我还从没见过上面的这种写法。后面又讲了Inner Class还有2种特殊的形式 **local classes和anonymous classes**

### 2.3 遮盖（Shadowing)

如果在一个特定作用域（例如一个内部类中或者是一个方法定义中）中的一个类型的声明（比如一个成员变量，或者是一个参数名称）与包含它的作用域中的另外一个类型定义重名了，那么内部的作用域中的声明就遮盖掉了外部的作用域中的声明。下面就是个例子：

```java
public class ShadowTest {

    public int x = 0;

    class FirstLevel {

        public int x = 1;

        void methodInFirstLevel(int x) {
            System.out.println("x = " + x);
            System.out.println("this.x = " + this.x);
            System.out.println("ShadowTest.this.x = " + ShadowTest.this.x);
        }
    }

    public static void main(String... args) {
        ShadowTest st = new ShadowTest();
        ShadowTest.FirstLevel fl = st.new FirstLevel();
        fl.methodInFirstLevel(23);
    }
}
```

运行的输出结果是：

```text
x = 23
this.x = 1
ShadowTest.this.x = 0
```

现在来解释一下，这个例子中，定义了3个名叫x的变量，首先是ShadowTest类的成员变量，然后是inner class FirstLevel的成员变量，然后是方法 methodInFirstLevel中的参数。

因为函数的参数的作用域是最内层的，所以直接打印x的值引用的是参数的值，也就是23。第二句用this，引用到了FirstLevel的实例的变量，也就是1。

下面使用类型名+this的方式，引用到FirstLevel的外部类的成员变量。这种写法，我也是第一次看到。这里引用的是就是ShadowTest类型的实例的变量中的x了。

```java
System.out.println("ShadowTest.this.x = " + ShadowTest.this.x);
```

### 2.4 序列化

这里提到了对于inner classes的序列化是强烈不推荐的，说了一大堆问题，没有仔细看。
