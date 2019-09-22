---
title: "Introduction about Maven the 3rd chapter" 
date: 2019-09-21
tag: maven
---


## 1. Introduction

In this chapter, we introduce a simple project created from scratch using the Maven Archetype plugin. This elementary application provides us with the opportunity to discuss some core concepts while you follow along with the development of the project.

为什么要从简单的东西开始呢，因为只有懂了简单的基础的东西，我们才能理解更复杂的东西。

## 2. Creating a Simple Project

we start a new Maven Project, use the Maven Archetype plugin from the cmd:

```cmd
mvn archetype:generate -DgroupId=org.sonatype.mavenbook -DartifactId=simple -Dpackage=org.sonatype.mavenbook -Dversion=1.0-SNAPSHOT

```

使用上面的命令的时候，如果是使用maven的默认配置，应该是从maven的默认的中心仓库来获取插件和依赖的，那样非常慢，然后发现aliyun是有Maven镜像的：

我找到的aliyun的镜像是：

```xml
<mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```

这样使用起来确实飞快，成功的创建了工程：

下面简单看以下这个quick start的工程的结构：

```cmd
C:\Users\Q\Desktop\simple>tree /F
文件夹 PATH 列表
卷序列号为 C047-AE20
C:.
│  pom.xml
│
└─src
    ├─main
    │  └─java
    │      └─org
    │          └─sonatype
    │              └─mavenbook
    │                      App.java
    │
    └─test
        └─java
            └─org
                └─sonatype
                    └─mavenbook
                            AppTest.java
```

1. 首先Maven的Archetype plugin创建了一个叫做simple的文件夹，这个文件夹对应到artifactId的值

2. 每一个project根目录里面都有一个pom.xml的文件来用描述这个工程，配置插件和声明依赖

3. 工程的源代码文件都放在src/main下面，测试的代码都放在src/test下面

在App.java文件中有如下的非常简单的Hello World的代码：

```java
package org.sonatype.mavenbook;

/**
 * Hello world!
 *
 */
public class App 
{
    public static void main( String[] args )
    {
        System.out.println( "Hello World!" );
    }
}

```

## 3. Build a Project

简单的build一个工程的方法就是 mvn install

我看到的输出如下：
```cmd
[INFO] Installing C:\Users\Q\Desktop\simple\target\simple-1.0-SNAPSHOT.jar to D:\mysdk\.m2\repository\org\sonatype\mavenbook\simple\1.0-SNAPSHOT\simple-1.0-SNAPSHOT.jar
[INFO] Installing C:\Users\Q\Desktop\simple\pom.xml to D:\mysdk\.m2\repository\org\sonatype\mavenbook\simple\1.0-SNAPSHOT\simple-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  01:11 min
[INFO] Finished at: 2019-09-22T22:04:16+08:00
[INFO] ------------------------------------------------------------------------
```

从这个输出可以看到，确实把我的项目编译成了jar文件，并且安装到了local本地的仓库中了

这个时候可以看到本地的工程目录里面出现了一个target文件夹，里面有生成的东西:

```cmd
C:\Users\Q\Desktop\simple>tree
文件夹 PATH 列表
卷序列号为 C047-AE20
C:.
├─src
│  ├─main
│  │  └─java
│  │      └─org
│  │          └─sonatype
│  │              └─mavenbook
│  └─test
│      └─java
│          └─org
│              └─sonatype
│                  └─mavenbook
└─target
    ├─classes
    │  └─org
    │      └─sonatype
    │          └─mavenbook
    ├─maven-archiver
    ├─maven-status
    │  └─maven-compiler-plugin
    │      ├─compile
    │      │  └─default-compile
    │      └─testCompile
    │          └─default-testCompile
    ├─surefire-reports
    └─test-classes
        └─org
            └─sonatype
                └─mavenbook
```

## 4. Simple Project Object Model

'simple'的工程的POM文件很简单。仅仅描述了工程的artifactId，groupId和version。

这里也提到了一个有趣的事情，就是不仅仅是我们眼前看到的这些属性是POM的属性，也有parent的属性，和Super-POM的属性：

所以想要看到所有生效的POM的属性，可以使用下面的命令：

```cmd
C:\Users\Q\Desktop\simple>mvn help:effective-pom
```

就可以看到所有的有效的POM的属性了
