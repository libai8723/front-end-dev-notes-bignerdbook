---
title: "Java Generic <11. Type Erasure> Reading the oracle tutorial" 
date: 2021-02-03

tag: "JLS&JVM Spec"
---
介绍了一下，java的泛型实际上是编译之前的，对于运行时来说，没有变化。

泛型的信息都在编译的过程中进行了转换和擦除。

<!--more-->

## 一、大体原则，有三条

1. 将泛型类型中所有的类型参数替换为他们的界限类型或者是Object，这样产生的bytecode都是只包含普通的类型，接口和方法。
2. 在必要的地方插入类型转换的语句，来保证类型的安全
3. 生成一些bridge method来支持扩展泛型类型下的多态（polymorphism）机制


## 二、泛型类型擦除

话不多说，直接看代码吧

```java
public class Node<T> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
}
```

因为是一个没有bound的Type Parameter所以擦除成下面的样子了

```java
public class Node {

    private Object data;
    private Node next;

    public Node(Object data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Object getData() { return data; }
    // ...
}
```

对于有上界的，就用上界来擦除

```java
public class Node<T extends Comparable<T>> {

    private T data;
    private Node<T> next;
    
//擦除为

public class Node {

    private Comparable data;
    private Node next;
```

## 三、泛型函数的类型擦除，与泛型类型的类型擦除一样，不写了

## 四、bridge method，编译器为了保证多态，会插入一些方法来保证多态。
