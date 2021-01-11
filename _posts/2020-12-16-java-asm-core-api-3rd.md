---
title: "The Java ASM library Core API----Interface and Components" 
date: 2020-12-16

tag: "JAVA ASM"
---

首先说一下这里的interface和component指的是ASM这个库针对classes的对应的编程的结构和组件组成。

ASM库中用来生成和转换编译的类文件的API是基于ClassVisitor这个抽象类的：

```java
public abstract class ClassVisitor {

    public ClassVisitor(int api);
    public ClassVisitor(int api, ClassVisitor cv);

    public void visit(int version, int access, String name, String signature, String superName, String[] interfaces);

    public void visitSource(String source, String debug);
    
    public void visitOuterClass(String owner, String name, String desc);
    
    AnnotationVisitor visitAnnotation(String desc, boolean visible);
    
    public void visitAttribute(Attribute attr);
    
    public void visitInnerClass(String name, String outerName, String innerName, int access);

    public FieldVisitor visitField(int access, String name, String desc, String signature, Object value);

    public MethodVisitor visitMethod(int access, String name, String desc, String signature, String[] exceptions);
    
    void visitEnd();
}
```

这里提到了abstract class，那就好好一下这个东西，既然使用了抽象类，那也就是说，可能有很多基本的功能在ClassVisitor中已经实现了，但是有很多抽象的方法就没有实现了，想要使用的话，就需要自己来实现这个抽象类了。

![StructureOfClassFiles](/front-end-dev-notes-bignerdbook/assets/img/StructureOfClassFile.png)

上面的ClassVisitor类中的对应的方法就是用来访问类中对应的部分（section）的，比如：

1. visit这个方法就是用来访问class层级的,所以对应的是上面表格中的类的信息,我们可以看到一下visit的参数: 这里的ersion

    ```java
        public void visit(int version, int access, String name, String signature, String superName, String[] interfaces) 
    ```

    * version - 类文件的版本. minor version 存储在16 most significant位中,major version存储在16 least significant位中.
    * access - 表示类的访问标志例如public, private等等,[see Opcodes](https://asm.ow2.io/javadoc/constant-values.html#org.objectweb.asm.Opcodes.ACC_PRIVATE). 也会表明这个类是否是deprecated或者是一个record?啥叫一个record.
    * name - 类的internal name
    * signature - 类的签名. 如果一个类不是一个范型的类,或者没有extend一个范型的类,或者实现一个范型的接口的话,那这个取值就是null
    * superName - 父类的inernal name
    * interfaces - 类inherited interface

2. @todo,对于注解这种元素,对应的visitAnnotation方法是什么意思呢?

