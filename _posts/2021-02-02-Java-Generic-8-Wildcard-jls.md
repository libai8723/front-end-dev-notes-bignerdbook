---
title: "Java Generic <8. Wildcards> Reading the oracle tutorial" 
date: 2021-02-02

tag: "JLS&JVM Spec"
---

通配符号，也就是问号，?

?代表了一个未知的类型

<!--more-->

## 一、通配符号用在什么情况下

1. 作为一个参数，字段或者本地变量的类型( as the type of a parameter, field, or local variable; )
2. 有的时候作为一个返回值类型，尽管这样做不太好

但是通配符号，不用于 范型函数调用的type argument，也不用作范型类实例创建的type argument，也不作为一个超类的type argument

下面讨论：

1. 上界通配符
2. 下界通配符
3. 通配符捕获

## 二、上界通配符号

比如下面的代码例子

The sumOfList method returns the sum of the numbers in a list:

```java
public static double sumOfList(List<? extends Number> list) {
    double s = 0.0;
    for (Number n : list)
        s += n.doubleValue();
    return s;
}
```

The following code, using a list of Integer objects, prints sum = 6.0:

```java
List<Integer> li = Arrays.asList(1, 2, 3);
System.out.println("sum = " + sumOfList(li));
```

A list of Double values can use the same sumOfList method. The following code prints sum = 7.0:

```java
List<Double> ld = Arrays.asList(1.2, 2.3, 3.5);
System.out.println("sum = " + sumOfList(ld));
```

从这里我们可以看出，List<? extends Number> list 这个类型的声明，可以接受很多类型，例如 List\<Integer>, List\<Double>等等。

## 三、无界限通配符号

直接使用?

这种情况是想要直接使用Object对应的函数，

```java
public static void printList(List<Object> list) {
    for (Object elem : list)
        System.out.println(elem + " ");
    System.out.println();
}
```

比如上面的代码只能打印List\<Object>类型的列表，没有办法接受List\<Integer>, List\<String>, List\<Double>, 因为它们不是List\<Object>的子类. 这时候想要写一个更通用的范型方法就要使用 List<?>:

```java
public static void printList(List<?> list) {
    for (Object elem: list)
        System.out.print(elem + " ");
    System.out.println();
}```

## 四、下界通配符号

看不出来有什么用途

```java
public static void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 10; i++) {
        list.add(i);
    }
}
```

就是可以接受 List\<Integer>, List\<Number>, List\<Object>

然后上面的代码就是可以往上面这三种List中插入Integer的数字。没有搞懂有什么作用
