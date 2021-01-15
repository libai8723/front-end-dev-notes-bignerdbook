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
private | Y | N | N | N

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

### line 263~272

```java
        maxStringLength = currentMaxStringLength;
        // The Classfile's access_flags field is just after the last constant pool entry.
        header = currentCpInfoOffset;

        // Allocate the cache of ConstantDynamic values, if there is at least one.
        constantDynamicValues = hasConstantDynamic ? new ConstantDynamic[constantPoolCount] : null;

        // Read the BootstrapMethods attribute, if any (only get the offset of each method).
        bootstrapMethodOffsets =
                hasBootstrapMethods ? readBootstrapMethodsAttribute(currentMaxStringLength) : null;
```

首先把常量池中utf-8 string的最大长度，存在实例变量的maxStringLength中，然后把已经处理到的文件的字节的offset放在实例变量的header中。
然后处理了2个我不太明白的东西，动态的常量和BootStrap的方法。 @todo

这样的话，构造函数就结束了。感觉非常简单，仅仅是把常量池的常量的index解析出来，放在一个实例变量的cpInfoOffsets数组中了。

## accept方法

### 函数签名 line 418~421

```java
public void accept(
            final ClassVisitor classVisitor,
            final Attribute[] attributePrototypes,
            final int parsingOptions) 
```

传入了一个classVisitor实例，用针对不同的class file的element进行对应的操作。

### line 422~438

```java
        Context context = new Context();
        context.attributePrototypes = attributePrototypes;
        context.parsingOptions = parsingOptions;
        context.charBuffer = new char[maxStringLength];

        // Read the access_flags, this_class, super_class, interface_count and interfaces fields.
        char[] charBuffer = context.charBuffer;
        int currentOffset = header;
        int accessFlags = readUnsignedShort(currentOffset);
        String thisClass = readClass(currentOffset + 2, charBuffer);
        String superClass = readClass(currentOffset + 4, charBuffer);
        String[] interfaces = new String[readUnsignedShort(currentOffset + 6)];
        currentOffset += 8;
        for (int i = 0; i < interfaces.length; ++i) {
            interfaces[i] = readClass(currentOffset, charBuffer);
            currentOffset += 2;
        }
```

context是一个上下文，在这个accept函数中使用，这个上下文还是挺有用的，出现在很多地方，还是很多函数调用的入参
这里首先是申请了一小块buffer使用，用来存字符串，然后获取了访问标识，然后读取了thisClass和superClass。然后读取了接口列表。
这里稍微看一下thisClass的读取的函数吧，看完之后发现很简单的，也就是跟踪了一下thisClass的常量池常量，然后一直追踪到对应的utf-8 String
下面读取接口的也是一样的。

### line 440~542

这一段一开始看的时候，我也很疑惑，都是啥玩意阿。后来一看，有道理，先把class层面的东西都搞定，在深入其他的元素来读取，这样比较思考起来比较整齐。

看来作者也是思考起来很整齐的，下面的代码读取的东西就是下面的东西了，先看 440 ~ 473 行的代码吧

```java
        // Read the class attributes (the variables are ordered as in Section 4.7 of the JVMS).
        // Attribute offsets exclude the attribute_name_index and attribute_length fields.
        // - The offset of the InnerClasses attribute, or 0.
        int innerClassesOffset = 0;
        // - The offset of the EnclosingMethod attribute, or 0.
        int enclosingMethodOffset = 0;
        // - The string corresponding to the Signature attribute, or null.
        String signature = null;
        // - The string corresponding to the SourceFile attribute, or null.
        String sourceFile = null;
        // - The string corresponding to the SourceDebugExtension attribute, or null.
        String sourceDebugExtension = null;
        // - The offset of the RuntimeVisibleAnnotations attribute, or 0.
        int runtimeVisibleAnnotationsOffset = 0;
        // - The offset of the RuntimeInvisibleAnnotations attribute, or 0.
        int runtimeInvisibleAnnotationsOffset = 0;
        // - The offset of the RuntimeVisibleTypeAnnotations attribute, or 0.
        int runtimeVisibleTypeAnnotationsOffset = 0;
        // - The offset of the RuntimeInvisibleTypeAnnotations attribute, or 0.
        int runtimeInvisibleTypeAnnotationsOffset = 0;
        // - The offset of the Module attribute, or 0.
        int moduleOffset = 0;
        // - The offset of the ModulePackages attribute, or 0.
        int modulePackagesOffset = 0;
        // - The string corresponding to the ModuleMainClass attribute, or null.
        String moduleMainClass = null;
        // - The string corresponding to the NestHost attribute, or null.
        String nestHostClass = null;
        // - The offset of the NestMembers attribute, or 0.
        int nestMembersOffset = 0;
        // - The offset of the PermittedSubclasses attribute, or 0
        int permittedSubclassesOffset = 0;
        // - The offset of the Record attribute, or 0.
        int recordOffset = 0;
```

那么都是什么意思呢，我们一个一个看吧： 首先是如果是有内部类相关的，那就是有对应的inner classes的attr，然后是如果是本身是在一个类的函数中声明的类，就会有enclosingMethod的attr，然后是signature是对应类有范型的情况。然后就是各种类上面的注解的情况。

@todo，需要继续阅读了。

