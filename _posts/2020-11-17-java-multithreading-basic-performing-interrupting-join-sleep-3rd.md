---
title: "Java Multithreading Basic 2nd Performing More Advanced Thread Tasks" 
date: 2020-11-17
tag: "Java Multithreading"
---

## Java Multithreading

### interrupting, joining and sleep

1. 这里提到的高级的特性也就是一个线程可以打断另外一个线程
2. 一个线程可以join到另外一个线程
3. 或者让一个线程进入睡眠

### 1 打断一个线程（interrupting）

@todo，看到这里的时候，发现自己应该先把Oracle Java Tutorial的文档看完。也就是这个链接：[Java Concurrency](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html
)

### 99 我的一个实验

看到Thread.sleep()是一个静态方法，所以我很好奇，如果我在main线程里面使用我启动的一个线程的reference来调用sleep()函数的话，是对应被启动的那个线程进入睡眠了呢，还是说main函数进入睡眠了呢，试一试就知道了

```java
import java.util.Date;
import java.text.SimpleDateFormat;

public class Main{
    public static void main(String[] args) {
        Runnable r = () -> {
            SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss Z");
            System.out.println(format.format(new Date()) + ": subThread say hello");
        };

        Thread th = new Thread(r);

        th.start();

        try{
           th.sleep(2000);
        }catch (InterruptedException e){
            System.out.println(e.getStackTrace());
        }
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss Z");
        System.out.println(format.format(new Date()) + ": Main Thread say hello");
    }
}
```

上面的代码看起来会非常的令人疑惑的，但是实际上从代码的运行来看，虽然用的是th这个对象的引用，但是实际上陷入sleep的是main线程：

```cmd
libai@libai-HP-EliteBook-820-G2:~/Documents/java-threading/0-1-call-sleep-on-thread-instance$ java Main
2020-11-17 23:16:06 +0800: subThread say hello
2020-11-17 23:16:08 +0800: Main Thread say hello
```

看来这个静态方法是有自己判断需要将哪一个线程投入睡眠状态的判断的。
