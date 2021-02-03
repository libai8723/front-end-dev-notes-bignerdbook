---
title: "Java Generic <9. Wildcards and Subtyping> Reading the oracle tutorial" 
date: 2021-02-03

tag: "JLS&JVM Spec"
---

当我们使用通配符号的时候，子类型之间的关系

<!--more-->

## 一、我们知道parameterized type之间基本是没有关系的

意思就是List<Number>和List<Integer>之间不是父类和子类的关系，他们是2个不同的类型。

如果我们想让泛型类型有父类和子类的关系的话，之前的章节说了，要保证type parameter是同一种类型的基础上

使用extends和implements来扩展或者实现基类，比如Collection<String> -> List<String> -> ArrayList<String>

## 二、使用通配符也可以实现另外一种父类和子类的关系。

比如

```java
List<? extends Integer> intList = new ArrayList<>();
List<? extends Number>  numList = intList;  // OK. List<? extends Integer> is a subtype of List<? extends Number>
```

因为使用了通配符，所以numList可以接受 List\<Number\>和List\<Integer\>，所以就实现了一种父类和子类的关系


下面给了一张图，可以好好理解一下

```text
                               +------------+
                               |            |
                               |   List<?>  |
                               |            |
                               +-----+------+
                                     ^
            +------------------------+----------------------+
            |                                               |
+-----------+---------------+                  +------------+--------------+
|                           |                  |                           |
|  List<? extends Number>   +<-------+         |  List<? super Integer >   |
|                           |        |   +---->+                           |
+------------+--------------+        |   |     +-------------+-------------+
             ^                       |   |                   ^
             |                       |   |                   |
             |                       |   |                   |
+------------+--------------+        |   |     +-------------+-------------+
|                           |        |   |     |                           |
|  List<? extends Integer>  |        |   |     |  List<? super Number   >  |
|                           |        |   |     |                           |
+------------+--------------+    +-------+     +-------------+-------------+
             ^                   |   |                       ^
             |                   |   |                       |
             |                   |   |                       |
+------------+--------------+    |   |         +-------------+-------------+
|                           |    |   |         |                           |
|  List<    Integer      >  +----+   +---------+  List<  Number         >  |
|                           |                  |                           |
+---------------------------+                  +---------------------------+
```
