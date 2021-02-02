---
title: "Java Generic <3. Generic Method> Reading the oracle tutorial" 
date: 2021-02-02

tag: "JLS&JVM Spec"
---

java范型第3章，generic method

<!--more-->

## 一、范型方法

什么是范型方法,简单来说范型方法就是一个方法定义了属于自己的类型参数。

static和non-static方法都可以使用类型参数，构造函数也可以使用类型参数。

考虑下面的代码

```java
public class Util {
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
}

public class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    public K getKey()   { return key; }
    public V getValue() { return value; }
}
```

一个工具类的静态方法就是定义了2个类型参数。

下面是使用案例

```java
Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.<Integer, String>compare(p1, p2);

// The type has been explicitly provided, as shown in bold. Generally, this can be left out and the compiler will infer the type that is needed:

Pair<Integer, String> p1 = new Pair<>(1, "apple");
Pair<Integer, String> p2 = new Pair<>(2, "pear");
boolean same = Util.compare(p1, p2);
```

可以看到如果要显式的调用一个范型方法，要在方法的前面指定具体的类型参数，或者是省略整个\<\>宝石语法。因为编译器可以推测出来。
