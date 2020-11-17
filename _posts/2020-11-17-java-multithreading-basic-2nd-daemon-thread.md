---
title: "Java Multithreading Basic 2nd Daemon Thread" 
date: 2020-11-17
tag: "Java Multithreading"
---

## Java Multithreading

### Daemon Thread 守护线程

Java对于线程的分类是2大类，也就是守护线程和非守护线程。下面是对于守护线程的定义

```text
A daemon thread is a thread that acts as a helper to a nondaemon thread and dies automatically when the application's last nondaemon thread dies so that the application can terminate.
```

守护线程是是针对非守护线程的一个helper，守护线程会在应用的最后一个非守护线程死掉的时候自动的死掉，从而让一个应用终止。

可以从下面的代码中判断一个线程是否是守护线程

```java
Thread t = new Thread(r);
System.out.println(t.isDaemon());
```

与Thread 对象关联的线程的默认都是非守护线程。我们可以通过 Thread.setDaemon(boolean isDaemon) 来把线程设置为守护线程。

特别要注意的事情是：

```text
An application will not terminate when the nondaemon default main therad terminates until all background nondaemon threads terminate.
```

一个程序当它的默认的主线程结束的时候，并不会退出，而是要等所有的后台的非守护线程都退出。而我们从本篇第一句话就知道了，当所有的非守护进行终止的时候，所有的守护进程也就终止了。

下面这个例子就很说明问题：

```java
public class DaemonThreadDemo{
    public static void main(String[] args) {
        boolean isDaemon = args.length != 0;

        Runnable r = () -> {
            Thread thd = Thread.currentThread();
            while (true) {
                System.out.printf("%s is %salive and in %s state\n",
                                    thd.getName(),
                                    thd.isAlive()?"":"NOT ",
                                    thd.getState());
            }
        };

        Thread t1 = new Thread(r);
        if(isDaemon)
            t1.setDaemon(true);

        Thread t2 = new Thread(r);
        if(isDaemon)
            t2.setDaemon(true);

        System.out.printf("%s is %salive and in %s state\n",
                            t1.getName(),
                            t1.isAlive()?"":"NOT ",
                            t1.getState());

        System.out.printf("%s is %salive and in %s state\n",
                            t2.getName(),
                            t2.isAlive()?"":"NOT ",
                            t2.getState());

        t1.start();
        t2.start();
    }
}
```

当上面的代码输入最少一个参数的时候，这时候t1和t2就被设定为守护线程，因为main主线程在启动t1和t2之后啥也没干就退出了，这个时候app的进程中就没有非守护线程了，所以这两个守护线程就非常快就退出了。

当输入没有命令行参数的时候，t1和t2就都是非守护线程，这个时候，它俩就陷入了无穷尽的循环了。所以很直观。

```cmd
libai@libai-HP-EliteBook-820-G2:~/Documents/java-threading$ java DaemonThreadDemo 1
Thread-0 is NOT alive and in NEW state
Thread-1 is NOT alive and in NEW state
Thread-0 is alive and in RUNNABLE state
Thread-0 is alive and in RUNNABLE state
Thread-0libai@libai-HP-EliteBook-820-G2:~/Documents/java-threading$ java DaemonThreadDemo 1
Thread-0 is NOT alive and in NEW state
Thread-1 is NOT alive and in NEW state
Thread-0 is alive and in RUNNABLE state
Thread-0 is alive and in RUNNABLE state
Thread-0 is alive and in RUNNABLE state
libai@libai-HP-EliteBook-820-G2:~/Documents/java-threading$ java DaemonThreadDemo 1
Thread-0 is NOT alive and in NEW state
Thread-1 is NOT alive and in NEW state
Thread-0 is alive and in RUNNABLE state
```

而且从实际的多次运行的输出来看，也是不一样的。有的时候t1和t2分配到的cpu的时间片段比较多，也就输出的比较多一点。而且可以看到第一次运行的时候，Thread-0的输出都没有输出完毕，就终止了。所以更加体验到了线程的特点。
