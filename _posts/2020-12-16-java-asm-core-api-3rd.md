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

@todo, 下午接着读
