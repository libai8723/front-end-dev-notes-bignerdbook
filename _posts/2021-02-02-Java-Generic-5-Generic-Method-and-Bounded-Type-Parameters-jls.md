---
title: "Java Generic <5. Generic Method and Bounded Type Parameters> Reading the oracle tutorial" 
date: 2021-02-02

tag: "JLS&JVM Spec"
---

有界限的类型参数和范型函数组合

<!--more-->

## 一、有界限的类型参数和范型函数组合

一句话，就是告诉你范型类型和有界限的类型参数是可以一起使用的，如下

```java
public static <T extends Comparable<T>> int countGreaterThan(T[] anArray, T elem) {
    int count = 0;
    for (T e : anArray)
        if (e.compareTo(elem) > 0)
            ++count;
    return count;
}
```

```<T extends Comparable<T>>```

这里面限定了T这个类型必须implements一个Comparable接口。

这个东西比较有趣，有点递归的意思
