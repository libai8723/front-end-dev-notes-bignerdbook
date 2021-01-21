---
title: "The Inner Classes" 
date: 2021-01-21

tag: "JLS&JVM Spec"
---


## 一、内部类 Inner Class

前面不是提到了吗，Static Nested Classes，感觉纯粹的好处也就是在代码和包结构上比较整洁，没别的了。

那么下面是一个例子，来演示一个Inner Class的使用。给出几点总结：

1. DataStructure 这个外部类，包含了一个构造器来创建一个创建一个整数的数组，数组的内容就是数组的index。并且包含了一个方法叫做printEven来打印偶数index的对应的指。

2. 声明了一个叫做EvenIterator的迭代器，这个迭代器实现了一个Iterator\<Integer\>的接口。迭代器是用来遍历数据结构的，基本上就是具备一个hasNext和next两个函数。

3. 还有一个main方法实例化一个DataStructure的对象，然后这个对象会调用printEven来打印数据的元素。

但是下面的代码的结构，我真的是看不出来有啥好处

```java
package innerclasses;

import java.util.Iterator;

public class DataStructure {

    private final static int SIZE = 15;
    private int[] array = new int[SIZE];

    public DataStructure() {
        for (int i = 0; i < array.length; i++) {
            array[i] = i;
        }
    }

    private class EvenIterator implements Iterator<Integer>{

        private int nextIndex = 0;

        @Override
        public boolean hasNext() {
            return (nextIndex <= SIZE - 1);
        }

        @Override
        public Integer next() {
            int value = array[nextIndex];
            nextIndex += 2;
            return value;
        }
    }

    public void printEven(){
        Iterator<Integer> it = this.new EvenIterator();

        while(it.hasNext()){
            System.out.println(it.next());
        }
    }

    public static void main(String[] args) {
        DataStructure ds = new DataStructure();
        ds.printEven();
    }
}

```

继续看local classes 和 anonymous classes吧 @todo
