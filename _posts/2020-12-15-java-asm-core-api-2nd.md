---
title: "The Java ASM library Core API----Class" 
date: 2020-12-15
tag: "JAVA ASM"
---

这本书的结构就是先介绍Core-API，Core-API又是按照Class，Method，Metadata等等章节来解释的。

## Classes 类文件

这个章节简单介绍一下class文件，和其ASM类库中的API编程接口

编译之后的类文件的结构是比较简单的（当然是相对于Natively Complied，比如C语言编译出来的.o文件），class文件保留了结构信息，还有几乎所有的符号信息（symbol 比如变量的名称），具体就是下面的东西：

1. 一部分（section）描述了类的限定符（Modifiers，比如public和private），类的名称，超类的名字，类实现的接口，类上面的注解。

2. 在class中声明的每一个field都有一部分描述这个field对应的限定符，名称，类型，和field对应的注解。

3. 在类中声明的每一个method和构造函数都有一个部分来描述，限定符，函数名称，返回值和参数的类型，和函数的注解。当然也包含这个函数编译之后的代码，这些编译之后的代码是java 字节码指令的序列。

## 源文件与class文件的区别

1. 编译后的class文件只包含一个class，而一个java源文件是可以包含多个class的，比如inner class，比如在一个文件中声明一个public class和若干个不带有限定符的class。

2. 编译后的class文件是不含有注释的。但是包含我们刚才说的那些个东西，类名字，field，method和code attr，但是自从java 5引入注解之后，这个叫做code attr的东西就没有啥用处了。

3. **编译后的class文件是不含有包名和import信息的，所以所有的name都是fully qualified的name**，这一点很重要了，这就和jvm的类加载的机制对应起来了。很有趣

还有一个重要的区别（class文件和java源文件）是编译后的class文件含有一个常量池，这个常量池是一个数组，这个数组包含了所有出现在class文件定义中出现的数值，字符串，type costant（这是啥？），这些常量仅仅在这个常量池中定义一次，在class文件的其他部分都使用他们的引用（index）。

上面就是一个class文件应该包含什么的概念性的描述，至于一个class文件严格的包含什么，应该参照jvm specification的class file章节。

另外一个很重要的不同就是，java types（java的类型）在classes文件中，和在java的源文件中表达的形式是不同的。

## 内部名称（internal names）

很多情况下一个类型，其实也就是一个类（class），或者是一个接口（interface），比如一个类的父类，一个类实现的interface，一个函数抛出的异常，都不能是原生类型或者是数组，而必须是class或者interface。这些类型在编译后的classes文件中，都是使用内部名称（internal names）来表达的，所谓的内部名称，其实也就是fully qualified name，只是把分隔用到的小圆点 **.** 换成了正斜杠。例如 java\lang\String

## 类型描述符（types descriptor）

上一个小节提到的内部名称是专门给到class和interface类型的。在所有的其他情况下，都是使用类型描述符的。

```text
注意：

这里需要补充一个知识，在看到这个段落的时候，我非常疑惑，

因为internal names和type descriptor都是可以用来

表达非primitive的类型的，那么什么时候使用internal names，

什么时候使用type descriptor呢，其实在ASM User Guide中
讲的不是很清楚的。

所以这里看了一下jvm specification中
关于classes files的描述： The Internal Form of Names : 
https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-4.html#jvms-4.2


其实jvm spec写的是比较优秀的，首先这些东西都叫做name，也就是名字，

因为我们要表达一个class，或者一个interface的名字，

还需要表达一个class field的名字，表达一个method的名字，局部变量的名字。

无论是什么名字的呢，都是一个名字，

但是可能是因为制定jvm spec的时候出于不同的考虑，

对于一个类class的名字，一个interface的名字，

和field，method，local ariable的名字，采用了不同的表达形式。

所以这个jvm spec的4.2小节才会其名字叫做 < the Internal Form of Names >

意思就是表达上面两种不同东西的时候，都用到了名字，
但是名字在classes files中的表达形式是不一样的。

前者，表达classes和interfaces的名字，在jvm spec中被叫做

1. Binary Class and Interface Names

这里的Binary Names，是从jls 13.1 关于二进制的兼容性中提出的，

我没有深究，从当前的水平来看，我们只要认为Binary Class and Interface Names

就是为了坚固兼容性而制定出的名字的规范，规则非常非常多，简单理解，

举个例子String在jls中的binary class name就是 java.lang.String

但是在jls中和jvm有些不同，在jvm中把句号换成了正向斜杠。例如java/lang/String

2. Unqualified Names

这一类名字就是针对methods，fields和局部变量的名字，

这类名字在classes file中被存储的时候，

就是使用的这个叫做Unqulified Names。Unqualified Names中不能出现4种字符，
分别是 .,[/ 就是句号，都好，左方括号，和正斜杠。

对于method的命名来说，做了进一步的约束，
不可以使用 <init>和<clinit>，也就是说method的名字中不能出现尖括号。
```


有了上面这段关于jvm spec的说明之后，Type Descriptor就比较清晰了，对于class和interface的来说要使用internal names，但是对于其他的类的成员，比如field，method，还有method中的局部变量来说，就使用Unqualified Names了，在ASM Guide中作者把这类名字叫做：Type Descriptor

还列举了一张表格

Java Type | Type Descriptor
------------ | -------------
boolean | Z
char | C
int | I
... | ...
Object | Ljava/lang/Object;
int[] | [I
Object[][] | [[Ljava/lang/Object;

从上面的表格中我们可以看出，对于原生类型的descriptor就是一个单个的大写字母，Z for boolean, C for char, B for byte, S for short, I for int, F for float, J for long and D for double

对于一个类的类型来说，它的descriptor就是在internal names前面增加一个大写的L字母，并且在后面跟随一个分号。

对于数组来说呢，就是在一个左方括号后面跟随一个类型（type）的internal name

## 方法描述符（method Descriptors）

方法描述符号的定义是在类型描述符的基础上定义的，而且定义的很简单，

A *method descriptor* is a list of type descriptors that describe the parameter types and the return type of a method, in a single string.

方法描述符就是有参数的类型描述符列表和返回值类型列表的字符串。

参数的类型描述符列表使用一对圆括号包裹，后面跟着返回值的类型描述符。

一个方法的描述符是不包含method name和parameter name的


Method declared in source file | Method Descriptor
-------------------------------|-------------------
void m(int i, float f) | (IF)V
int m（Obejct m) | (Ljava/lang/Object;)I

到这里就是讲完了classes的大致的结构了。下面要讲interfaces 和 components了
