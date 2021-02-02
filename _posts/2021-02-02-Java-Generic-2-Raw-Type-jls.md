---
title: "Java Generic <2. Raw Type> Reading the oracle tutorial" 
date: 2021-02-02

tag: "JLS&JVM Spec"
---

java范型第二章，raw type

<!--more-->

## 一、Raw Type

什么是Raw Type，Raw Type就是一个范型类型但是还没有给予其任何类型参数，例如针对下面的范型类型

```java
public class Box<T> {
    public void set(T t) { /* ... */ }
    // ...
}
```

下面的语句就创建了一个参数化的类型，叫做Box\<Integer\>

```java
Box<Integer> intBox = new Box<>();
```

如果在代码中类型参数被省略的话，我们就创建了一个raw type

```java
Box rawBox = new Box();
```

### 1.1 怎么使用raw type

原始类型的存在，主要是因为遗留代码的存在，例如Collections对应的类，出现在jdk5.0之前，那个时候还没有范型。那个时候主要都直接操作object

下面这种把parameterized type赋值给raw type是安全的，因为String是Object

```java
Box<String> stringBox = new Box<>();
Box rawBox = stringBox;               // OK
```

反之把raw type赋值给到parameterized type是不可以的，不是所有的object都是String

```java
Box rawBox = new Box();           // rawBox is a raw type of Box<T>
Box<Integer> intBox = rawBox;     // warning: unchecked conversion
```
