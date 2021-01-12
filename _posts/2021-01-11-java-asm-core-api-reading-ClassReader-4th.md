---
title: "The Java ASM library Core API----Interface and Components----Extended Reading ClassReader" 
date: 2021-01-11

tag: "JAVA ASM"
---

为什么狠下心阅读这个ClassReader的类呢，是因为看user guide感觉看懂了，但是又觉得没有看懂

总有一种似是而非的感觉，所以还是认真阅读一下吧，践行learn java asm the hard way

<!--more-->

## Constructor

有很多种略微不同的构造器，但是最终都指向了188行的构造函数，也就是下面的声明:

### 构造函数的 Signature

```java
ClassReader(final byte[] classFileBuffer, final int classFileOffset, final boolean checkClassVersion);
```

首先看到这是一个没有access flag修饰的构造函数，按照Java Tutorial的讲解：这个叫做默认的修饰符，这个默认修饰符比private稍微强一点，但是比protected弱一些

Modifier | Class | Package | Subclass | World
---------|-------|---------|----------|-------
public | Y | Y | Y | Y
protected | Y | Y | Y | N
no modifier | Y | Y | N | N
private | Y | Y | Y | Y

### line 194~197

做了class文件的版本的校验，就是检查文件头部的major-version和minor-version是否合适。

### line 198~202

```java
        int constantPoolCount = readUnsignedShort(classFileOffset + 8);
        cpInfoOffsets = new int[constantPoolCount];
        constantUtf8Values = new String[constantPoolCount];
```

因为常量池的大小的count是从第9个字节开始的，占用2个字节。然后放在ClassReader的实例变量中，一个是cpInfoOffsets用来存各种常量的开始的index offset的数字，然后又创建了一个String的数组，不知道这个干什么的用途。

然而我看了一下在constantUtf8Values在构造函数中除了上面初始化了一个String数组之外，再也没有其他地方引用了，估计不是在构造函数里面解析具体的常量池的内容的。

### line 207~262

```java
        int currentCpInfoIndex = 1;
        int currentCpInfoOffset = classFileOffset + 10;
        int currentMaxStringLength = 0;
        boolean hasBootstrapMethods = false;
        boolean hasConstantDynamic = false;
        // The offset of the other entries depend on the total size of all the previous entries.
        while (currentCpInfoIndex < constantPoolCount) {
            cpInfoOffsets[currentCpInfoIndex++] = currentCpInfoOffset + 1;
            int cpInfoSize;
            switch (classFileBuffer[currentCpInfoOffset]) {
                    ...
                }
        }
```
