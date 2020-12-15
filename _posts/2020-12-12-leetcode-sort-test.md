---
title: "Test the performance of sort primitive int array and object wrapped int array" 
date: 2020-12-12
tag: "leetcode basis"
---

## 排序的简单的测试

对于排序一个原生类型，比如int类型的数组，还有一个对象包装的对象数组，哪一个更快呢，下面的代码可以看出一些端倪

对于一个1千万个int类型的随机数组，和生成一个1千万个对象的对象数组的排序，单纯从排序的时间来看是没有太大的差距的，一个10ms左右，一个20ms左右，没有数量级的差距

但是申请一个1千万的int的数组就比申请一个1千万的对象要快太多了

输出如下

```text
new an int arr:14
sort int arr:17
init obj arr:3012
init obj arr and sort:24
```


```java
import java.util.Arrays;
import java.util.Date;

public class SortTest {
    public static void main (String[] args) {

        Date s0 = new Date();
        int[] arr = new int[10000000];
        Date s00 = new Date();
        System.out.println("new an int arr:" + (s00.getTime()- s0.getTime()));

        initArr(arr);

        Date s1 = new Date();
        Arrays.sort(arr);
        Date s2 = new Date();

        System.out.println("sort int arr:" + (s2.getTime() - s1.getTime()));

        ObjectWrap[] wrap = initObjArr(arr);

        Date s3 = new Date();

        System.out.println("init obj arr:" + (s3.getTime() - s2.getTime()));

        Arrays.sort(wrap);
        Date s4 = new Date();
        System.out.println("init obj arr and sort:" + (s4.getTime() - s3.getTime()));
    }


    public static ObjectWrap[] initObjArr(int[] arr){
        ObjectWrap[] re = new ObjectWrap[arr.length];
        for (int i = 0; i < arr.length; i++) {
            re[i] = new ObjectWrap(arr[i]);
        }
        return re;
    }

    public static void initArr(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            arr[i] = (int)Math.random() * 10000;
        }
    }
}

class ObjectWrap implements Comparable{
    int val;

    public ObjectWrap (int val) {
        this.val = val;
    }

    @Override
    public int compareTo (Object o) {
        ObjectWrap other = (ObjectWrap) o;
        if(this.val < other.val)
            return 1;
        else if(this.val > other.val)
            return -1;
        else
            return 0;
    }
}
```
