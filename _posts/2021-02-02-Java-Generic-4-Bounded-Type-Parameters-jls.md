---
title: "Java Generic <4. Bounded Type Parameters> Reading the oracle tutorial" 
date: 2021-02-02

tag: "JLS&JVM Spec"
---

有界限的类型参数

<!--more-->

## 一、有界限的类型参数

很多时候你想要限定那些类型参数可以传输进来，使用下面的语法

```java
    public <U extends Number> void inspect(U u){
        System.out.println("T: " + t.getClass().getName());
        System.out.println("U: " + u.getClass().getName());
    }
```

看到\<U extends Number\> 这个形式的意思是，U这个类型要么是extends了Number类型，要么是implements了Number类型，所以就限定住了。

## 二、多重边界

```<T extends B1 & B2 & B3>```

上面就是多重限定，如果B1，B2，B3中又一个类型，那一定是B1
