---
title: "Java Generic <6. Generic, Inheritance and Subtype> Reading the oracle tutorial" 
date: 2021-02-02

tag: "JLS&JVM Spec"
---

范型、继承和自类型

<!--more-->

## 一、参数化的类型是比较特殊的东西，下面解释一下

对于常见的java的类型来说，我们可以把一个子类型的引用赋值给到父类型的引用上，这个是很安全，也很自然的

```java
Object someObject = new Object();
Integer someInteger = new Integer(10);
someObject = someInteger;   // OK
```

对于一个已经类型参数化的范型类型，来说，往里传递一些子类型也是没有问题的

```java
Box<Number> box = new Box<Number>();
box.add(new Integer(10));   // OK
box.add(new Double(10.1));  // OK
```

这样也是没有问题的，但是考虑一下下面的函数的签名，它接收的是什么类型的呢？

```java
public void boxTest(Box<Number> n) { /* ... */ }
```

我们可以把```Box<Integer>```或者```Box<Double>```的给到它吗？答案是不行的，因为

```Box<Integer>```和```Box<Double>```和```Box<Number>```是三种不同的类型，不是子类的关系。

```text
+-------------+           +-----------------+
|             |           |                 |
|   Number    |           |   Box<Number>   |
|             |           |                 |
+------+------+           +--------+--------+
       ^                           ^
       |                           |  WRONG!!!!!
       |                           |
+------+------+           +--------+--------+
|             |           |                 |
|   Integer   |           |   Box<Integer>  |
|             |           |                 |
+-------------+           +-----------------+

               +---------+
               |         |
               |  Object |
               |         |
               +---^-----+
                   |
             +-----+-------------+
             |                   |
             |                   |
     +-------+-----+   +---------+----+
     |             |   |              |
     | Box<Number> |   | Box<Integer> |
     |             |   |              |
     +-------------+   +--------------+
```

上面这张图应该就很好的诠释了这个意思了。

## 二、范型类和它的子类

我们可以定义一个范型class或者interface的子类，但是是通过extends或者implements字句来实现的。

使用Collections类来打比方的话就是，ArrayList\<E\> 实现了 List\<E\>, 而List\<E\> 扩展了 Collection\<E\>.

所以ArrayList\<String\> 的List\<String\>的子类型， 而List\<String\>是Collection\<String\>的子类型

只要我们没有变更类型参数，在这些子类型之间保持一致就可以了

```text
+--------------------+
|                    |
| Collection<String> |
|                    |
+---------+----------+
          ^
          |
          |
+---------+----------+
|                    |
| List<String>       |
|                    |
+---------+----------+
          ^
          |
          |
+---------+----------+
|                    |
| ArrayList<String>  |
|                    |
+--------------------+
```

现在我们假想一下，我们定义一个自己的2个type parameters的类型，

```java
interface PayloadList<E,P> extends List<E> {
  void setPayload(int index, P val);
  ...
}
```

那么当P这个类型取不同类型的时候，我们可以得到下面的子类关系。

```text
                                     +--------------------+
                                     |                    |
                                     | Collection<String> |
                                     |                    |
                                     +---------+----------+
                                               ^
                                        +--------------+
                                        |      |       |
               +----------------------->+ List<String> +<---------------------------+
               |                        |              |                            |
               |                        +------^-------+                            |
               |                               |                                    |
               |                               |                                    |
+--------------+---------------+  +------------+-----------------+ +----------------+-------------+
|                              |  |                              | |                              |
| PayloadList<String, String>  |  | PayloadList<String, Integer> | | PayloadList<String, Integer> |
|                              |  |                              | |                              |
+------------------------------+  +------------------------------+ +------------------------------+
```
