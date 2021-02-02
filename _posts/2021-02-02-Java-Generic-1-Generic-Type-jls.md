---
title: "Java Generic <1. Generic Type> Reading the oracle tutorial" 
date: 2021-02-02

tag: "JLS&JVM Spec"
---

java的范型，因为要考试了，赶紧了解一下

<!--more-->

## 一、范型类型 Generic Type

简单来说范型类型就是只是可以用类型参数（type parameters）来定义的类型。下面就是一个甲简单的例子

```java
/**
 * Generic version of the Box class.
 * @param <T> the type of the value being boxed
 */
public class Box<T> {
    // T stands for "Type"
    private T t;

    public void set(T t) { this.t = t; }
    public T get() { return t; }
}
```

可以看到类型参数，使用大写的T来表示，按照要求，范型类型的定义中，类型参数跟在类型的名称的后面。

### 1.1 类型参数的命名的约定

1. E : Element
2. K : Key
3. N : Number
4. T : Type
5. V : Value
6. S,U,V : 2nd~4th Types

### 1.2 调用和实例化一个范型类型

```java
Box<Integer> integerBox;
```

### 1.3 宝石语法 Diamond

在JavaSE 7和之后的版本中，如果编译器可以推测出对应的类型的化，是可以省略类型参数的。

```java
Box<Integer> integerBox = new Box<>();
```

### 1.4 多个类型参数Type Parameters

就像标题说的，可以支持多个类型参数

```java
public interface Pair<K, V> {
    public K getKey();
    public V getValue();
}
```

其实就是对应了2种不同的类型参数

### 1.5 参数化的类型就是一种类型

当一个范型类型被invoke之后就被确定类型了，就不是后面要讲的raw type了，这个type本身也可以作为一种类型参数，如下面的例子

```java
OrderedPair<String, Box<Integer>> p = new OrderedPair<>("primes", new Box<Integer>(...));
``
