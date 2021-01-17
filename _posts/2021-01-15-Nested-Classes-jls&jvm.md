---
title: "The Nested Classes" 
date: 2021-01-15

tag: "JLS&JVM Spec"
---


## 内嵌类 Nested Class

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

## Nested Class在Oracle文档网站上的阅读

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

@todo，太晚了，需要赶紧回家了，明天继续阅读吧。

