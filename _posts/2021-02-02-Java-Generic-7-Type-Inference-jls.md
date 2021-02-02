---
title: "Java Generic <7. Type Inference> Reading the oracle tutorial" 
date: 2021-02-02

tag: "JLS&JVM Spec"
---

类型推断，这里是按照类型推断与范型函数，与范型类型实例化，与范型/非范型类型的范型构造器三部分来讲述。

<!--more-->

## 一、什么是类型推断呢

一句话来说，java编译器会检查每一个函数的调用和与其相关的声明，来决定类型参数是什么，让整个函数的调用是合法的。推测的算法，会从实际参数的类型，或者从函数返回的值的类型来进行推测。最后，推测算法会选择最specific的类型。

## 二、类型推断与范型方法

```java
public class BoxDemo {

  public static <U> void addBox(U u, java.util.List<Box<U>> boxes) {
    Box<U> box = new Box<>();
    box.set(u);
    boxes.add(box);
  }

  public static <U> void outputBoxes(java.util.List<Box<U>> boxes) {
    int counter = 0;
    for (Box<U> box: boxes) {
      U boxContents = box.get();
      System.out.println("Box #" + counter + " contains [" +
             boxContents.toString() + "]");
      counter++;
    }
  }

  public static void main(String[] args) {
    java.util.ArrayList<Box<Integer>> listOfIntegerBoxes =
      new java.util.ArrayList<>();
    BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes);
    BoxDemo.addBox(Integer.valueOf(20), listOfIntegerBoxes);
    BoxDemo.addBox(Integer.valueOf(30), listOfIntegerBoxes);
    BoxDemo.outputBoxes(listOfIntegerBoxes);
  }
}
```

上面的代码中，U这个类型是针对方法的type parameter，可以看到在调用的时候，可以显式的调用，也可以省略type argument如下

```BoxDemo.<Integer>addBox(Integer.valueOf(10), listOfIntegerBoxes);```

或者是

```BoxDemo.addBox(Integer.valueOf(10), listOfIntegerBoxes);```

## 三、类型推断与范型类型的实例化

这种是我们最经常使用的，就是面对HashMap这种类型

```Map<String, List<String>> myMap = new HashMap<String, List<String>>();```

上面是完整的写法，下面是省略的宝石写法

```Map<String, List<String>> myMap = new HashMap<>();```

这里要注意的是，如果需要让编译器去进行推断的话，则必须要加上宝石语法


## 四、类型推断与范型类或者非范型类的范型构造器

这个标题实在是太绕口了，原文是Type Inference and Generic Constructors of Generic and Non-Generic Classes

意思就是无论是不是范型类，都可以拥有一个范型构造器，然后这个构造器定了type parameter，在实际的使用中，编译器会推断具体的类型参数。

下面这个例子比较晦涩了

```java
class MyClass<X> {
  <T> MyClass(T t) {
    // ...
  }
}
```

```new MyClass<Integer>("")```

可以看到Integer是针对范型类MyClass的X类型参数的。编译器会推断T的类型是String，因为我们在调用构造器的时候，传入了一个String类型的对象。


```MyClass<Integer> myObject = new MyClass<>("");```

如果是java7以后的编译器，可以使用宝石语法，推断出MyClass的Type Argument的类型。


## 五、目标类型引导了编译器的推断

```static <T> List<T> emptyList();```
考虑上面的List.emptyList()的函数，在下面的调用中

```List<String> listOne = Collections.emptyList();```

编译发现，目标希望得到一个List\<String\>类型的对象，那么编译器就会把对应的范型函数的T类型推断为String

当然我们可以显式的写出来

```List<String> listOne = Collections.<String>emptyList();```

java7相比与java8在类型推断上有点傻乎乎的。
