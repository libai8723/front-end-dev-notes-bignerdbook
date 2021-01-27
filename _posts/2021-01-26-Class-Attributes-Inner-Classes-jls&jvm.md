---
title: "Class Level Attributes——InnerClasses Attributes" 
date: 2021-01-26

tag: "JLS&JVM Spec"
---

Class file format， class level attributes —— Inner Classes
这一章主要是在看到了java asm的ClassReader类的accept方法在解析class级别的属性（attributes）之后，觉得应该大概了解一下
本章看 Inner classes Attr

<!--more-->

## 一、本地类 Local Class

[Inner Classes Attributes](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-4.html#jvms-4.7.6)

原始的定时是这么说的，InnerClasses attibute是一个类的属性表中的变长的属性。如果一个类或者接口的常量池中的包含了一个CONSTANT_Class_info，并且这个CONSTANT_Class_info的entry指向的类或者接口并不是一个包的成员（意指是一个class的成员），那么当前的类就必须包含一个InnerClasses属性，在这个类的属性表里面。

一个InnerClasses Attr的结构如下：

```java
InnerClasses_attribute {
    u2 attribute_name_index;
    u4 attribute_length;
    u2 number_of_classes;
    {   u2 inner_class_info_index;
        u2 outer_class_info_index;
        u2 inner_name_index;
        u2 inner_class_access_flags;
    } classes[number_of_classes];
}
```

我们来实际找一个例子看一下，下面是我这边写了一个非常简单的例子：

```java
package pkgname;

public class Out{
    class InA{

    }
    class InB{
        
    }
}
```

下面来做一个比较详细的分析吧：

```text
$ xxd Out.class

00000000: cafe babe 0000 0037 0015 0a00 0300 1007  .......7........
00000010: 0011 0700 1207 0013 0100 0349 6e42 0100  ...........InB..
00000020: 0c49 6e6e 6572 436c 6173 7365 7307 0014  .InnerClasses...
00000030: 0100 0349 6e41 0100 063c 696e 6974 3e01  ...InA...<init>.
00000040: 0003 2829 5601 0004 436f 6465 0100 0f4c  ..()V...Code...L
00000050: 696e 654e 756d 6265 7254 6162 6c65 0100  ineNumberTable..
00000060: 0a53 6f75 7263 6546 696c 6501 0008 4f75  .SourceFile...Ou
00000070: 742e 6a61 7661 0100 0b4e 6573 744d 656d  t.java...NestMem
00000080: 6265 7273 0c00 0900 0a01 000b 706b 676e  bers........pkgn
00000090: 616d 652f 4f75 7401 0010 6a61 7661 2f6c  ame/Out...java/l
000000a0: 616e 672f 4f62 6a65 6374 0100 0f70 6b67  ang/Object...pkg
000000b0: 6e61 6d65 2f4f 7574 2449 6e42 0100 0f70  name/Out$InB...p
000000c0: 6b67 6e61 6d65 2f4f 7574 2449 6e41 0021  kgname/Out$InA.!
000000d0: 0002 0003 0000 0000 0001 0001 0009 000a  ................
000000e0: 0001 000b 0000 001d 0001 0001 0000 0005  ................
000000f0: 2ab7 0001 b100 0000 0100 0c00 0000 0600  *...............
00000100: 0100 0000 0300 0300 0d00 0000 0200 0e00  ................
00000110: 0f00 0000 0600 0200 0400 0700 0600 0000  ................
00000120: 1200 0200 0400 0200 0500 0000 0700 0200  ................
00000130: 0800 00                                  ...
```

因为不是很好排版，所以直接使用excel截图的方式来展示了

![Constants Pool](/front-end-dev-notes-bignerdbook/assets/img/cp_of_outclass.png)

这是比较常规的常量池的东西了，没啥特别的。

然后下面就是class 级别的attribute了

![class attribute Inner Classes](/front-end-dev-notes-bignerdbook/assets/img/class_attributes.png)

可以看到，按照开篇的说明来看的话，因为Out这个类引用了两个类分别是 InA和InB，而这两个类又不是package的成员，所以就会有一个class级别的Attribute来描述引用的类的信息，称为InnerClasses

而且我观察到，InnerClasses貌似总是和NestMember一起出现，所以下一篇就看NestMembers

@todo，而且我要做一个简单的class级别的匹配器，来简化我写文章，直接从字节码匹配做起吧。
