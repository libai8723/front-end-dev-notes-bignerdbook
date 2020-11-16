---
title: "Java Multithreading Basic 1th" 
date: 2020-11-16
tag: "Java Multithreading"
---

## Java Multithreading

### Basic Knowledge

为什么要看多线程呢，因为之前在看分布式中间件的时候，发现其实分布式中间件的思想都是源于单机的多线程原理，所以想要把这方面的基础知识学习的更牢固一些。

所以我找了一本电子书：< Java Threads and the Concurrency Utilities >

有些定义还是满有趣的：

```text
Java Applications executes via threads, which are independent paths of execution through an application's code
```

Java程序是以线程的方式来执行的，而线程就是一些程序代码执行的独立的路径。

```text
Each Java Application has a default main thread that executes the main() method
````

每一个Java程序都有一个默认的主线程用来执行main函数

```text
These threads execute code sequences encapsulated in objects that are known as Runnables.
```

我们可以创建额外的线程来负责一些对于时间敏感的人物，这些线程执行的代码序列被封装在一类叫做Runnable类型的对象中。所以从这个描述来看，抽象的还是蛮好的。Thread是代表和抽象OS的线程，而Runnable是一些可以被线程所执行的代码序列。这就更进一步解释了为什么有了Thread还需要Runnable。

```text
The JVM gives each thread its own JVM Stack to prevent threads from interfering with each other
```

java虚拟机会为每一个线程分配一个它自己的JVM Stack，避免线程之间的相互影响。

下面这段话就是比较精髓的东西了，讲述一下Thread和Runnable的关系：

```text
The Thread class provides a consistent interface to the underlying operating system's thread architecture.(The OS is typically reponsible for creating and managing threads.) A single operating system thread is associated with a Thread object.

The Runnable interface supplies the code to be executed by the thread that's associated with a Thread object. The code is located in Runnable's run() method.
```

上面的话简单来说，Thread提供了横跨不同操作系统的线程的一个一致性的封装，这样我们就不需要管下层的操作系统的架构了。一个操作系统的线程和一个thread对象对应。

Runnable接口提供了需要被线程所执行的代码，代码放在run方法中。

关于创建Runnable的方法，书上说有两种

```java
public class Main{
    public static void main(String[] args) throws Exception{
        Runnable r = new Runnable(){
            @Override
            public void run(){
                System.out.println("hello from a thread");
            }
        };

        Thread t = new Thread(r);
        t.start();
        Thread.sleep(100);
        System.out.println("hello from the main thread");
    }
}
```

上面是第一种，说是创建一个匿名类，其实搞一个非匿名类也是可以的。只不过匿名类的方式更加的简短一些。

上面的代码在编译之后，出现了一个匿名类，具体如下：

```shell
libai@libai-HP-EliteBook-820-G2:~/Documents/java-threading$ ls
'Main$1.class'   Main.class   Main.java
```

第一个带有$符号的就是匿名类了。@todo，这里应该复习一下Java的匿名类。

然后说java8之后因为引入了lambda表达式，所以可以更简化，具体代码如下：可以看出非常的简单，因为Runnable是一个functional interface所以才可以用下面的写法

```java
public class Main{
    public static void main(String[] args) throws Exception{
        Runnable r = () -> System.out.println("hello from the new thread.");

        Thread t = new Thread(r);
        t.start();
        Thread.sleep(100);
        System.out.println("hello from the main thread");
    }
}
```

而且编译出来的结果真的不一样，没有匿名类了。@todo，所以这里需要去看lambda表达式。

```bash
libai@libai-HP-EliteBook-820-G2:~/Documents/java-threading$ ls
Main.class  Main.java
```

### Thread State

线程的状态。线程的状态包括了：

1. 线程的名字
2. alive or dead
3. 线程的 execution state
4. 线程的优先级 Priority
5. 守护线程还是非守护线程

#### 获得线程的名称

```java
public class Main{
    public static void main(String[] args) throws Exception{
        Runnable r = () -> System.out.println("hello from: " + Thread.currentThread().getName());

        Thread t = new Thread(r);
        t.start();
        Thread.sleep(100);
        System.out.println("hello from： " + Thread.currentThread().getName());
    }
}
```

```cmd
libai@libai-HP-EliteBook-820-G2:~/Documents/java-threading$ java Main
hello from: Thread-0
hello from： main
```

可以看出来在我默认没有修改线程的名字的时候，主线程就叫做main了，我自己创建出来的线程是以Thread-数字的方式来命名的。

#### 线程的生死 Alive or Dead

这个概念和书中提到的execution state相比有点相似，但是实际上是两个不同的概念。Alive 和 Dead 是针对lifespan的概念来说的。

按照书上的原话来说，一个线程从调用start()之后到离开run()方法之前，都是Alive的状态的。除此之外都是Not Alive的状态。毕竟是一个二值化的系统。使用函数测试的是 isAlive()，也就是说当返回为true的时候是没问题的，剩下的只能叫做非Alive的状态。

```java
public class Main{
    public static void main(String[] args) throws Exception{
        Runnable r = () -> System.out.println("hello from: " + Thread.currentThread().getName());

        Thread t = new Thread(r);
        if(t.isAlive())
            System.out.println(t.getName() + "is Alive");
        else
            System.out.println(t.getName() + "is Dead");

        t.start();
        System.out.println(t.isAlive());
        Thread.sleep(100);
        System.out.println(t.isAlive());
        System.out.println("hello from： " + Thread.currentThread().getName());
    }
}
```

所以上面的代码的输出是：

```cmd
libai@libai-laotop$ java Main
Thread-0is Dead
true
hello from: Thread-0
false
hello from： main
```

### 线程的 Execution State

从名字可以看出来是执行的状态，所以和Alive还是区别很明显的。从API文档也是非常明确的：

1. Thread.State.NEW: 表是一个线程还没开始呢
2. Thread.State.RUNNABLE: 代表一个可以运行的线程。这个线程正在jvm中执行，但是正在等待资源，例如cpu
3. Thread.State.BLOCKED: 线程阻塞在一个monitor lock上了
4. Thread.State.WAITING: 线程正在等待另外一个线程
5. Thread.State.TIMED_WAITING: 线程正在等待一个指定的时间.
6. Thread.State.TERMINATED: 已经终止的一个线程。

该阅读守护线程和非守护线程了：@todo
