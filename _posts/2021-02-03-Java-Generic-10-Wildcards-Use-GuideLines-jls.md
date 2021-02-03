---
title: "Java Generic <10. Guidelines for Wildcards Usage> Reading the oracle tutorial" 
date: 2021-02-03

tag: "JLS&JVM Spec"
---
使用通配符的时候，注意的原则，也就是很多中文博客里面说的PECS，Producer Extends， Consumer Super。PECS是这个Guide Lines的其中一部分

<!--more-->

## 一、使用通配符真的好复杂，所以官方给了指导原则

为了让我们的讨论更加的简单，我们假设一个变量基本上具备2种功能的其中1种。

1. 作为一个 In 的变量，为我们的代码提供数据，例如在一个 ```copy(src,dest)``` 的代码中，src变量提供了数据，我们认为这是一个In类型的变量
2. 作为一个 Out 类型变量，还是上面的函数调用，dest作为数据的接收方，我们就认为她是一个Out类型的变量

下面就是GuideLines了

1. 对于一个In类型变量，使用上界通配符，也就是 \<? extends T\>
2. 对于一个Out类型变量，使用下界通配符，也就是 \<? super T\>
3. 在In变量的情况下，如果只使用Object中定义的方法就能搞定，那就用无界限通配符 <?>
4. 如果一个变量既作为In类型变量，又作为Out类型变量，则不要使用通配符


其中1,2就是说的PECS的原则。因为PE的情况下，无法往Collection里面写入。而CS无法进行读取。

为什么无法写入呢，有一个博客讲的蛮好的 (泛型总结-通配符)[https://segmentfault.com/a/1190000005337789]

```java
class Fruit {}
class Apple extends Fruit {}
class Jonathan extends Apple {}
class Orange extends Fruit {}

public class CompilerIntelligence {
    public static void main(String[] args) {
        List<? extends Fruit> flist =
        Arrays.asList(new Apple());
        Apple a = (Apple)flist.get(0); // No warning
        flist.contains(new Apple()); // Argument is ‘Object’
        flist.indexOf(new Apple()); // Argument is ‘Object’
        
        //flist.add(new Apple());   无法编译

    }
}
```

上面的代码怎么理解呢，List\<? extends Fruit\>这个代表了很多东西，按照上面的定义，代表了List\<Fruit\>、或者List\<Apple\>、或者List\<Jonathan\>、或者List\<Orange\>。

所以我们并不知道具体后面是啥东西

所以我们想要往里面塞东西的时候，可能后面是一个List\<Apple\>，但是我们可能试图往里面塞入 Orange，所以编译器也推断不出来，所以不能塞东西了。

进一步看一下List的定义的方法签名

```java
public boolean add(E e)
public boolean contains(Object o)
public int indexOf(Object o)
```

add方法使用的是一种E的类型参数，当List声明为List<? extends Fruit>的时候，这个E就没有办法推断其类型了，所以没有办法写入了

这就是不能写入的原因了。

下面是摘抄了一段

通配符的另一个方向是 **超类型的通配符**: \<? super T\>，T 是类型参数的下界。使用这种形式的通配符，我们就可以 ”传递对象” 了。还是用例子解释：

```java
public class SuperTypeWildcards {
    static void writeTo(List<? super Apple> apples) {
        apples.add(new Apple());
        apples.add(new Jonathan());
        // apples.add(new Fruit()); // Error
    }
}
```

下面是解释：

writeTo 方法的参数 apples 的类型是 List<? super Apple>，它表示某种类型的 List，这个类型是 Apple 的基类型。

也就是说，我们不知道实际类型是什么，但是这个类型肯定是 Apple 的父类型，比如Apple，比如Fruit

因此，我们可以知道向这个 List 添加一个 Apple 或者其子类型的对象是安全的，这些对象（Apple，Jonathan）都可以向上转型为 Apple。

所以写入是安全的。


所以看起来确实有点绕啊，还是记住PECS
