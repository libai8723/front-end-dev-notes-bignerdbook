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

要让上面的程序运行起来也是很简单的，直接切换到target的对应目录就行了：

```cmd
$ java -cp target/simple-1.0-SNAPSHOT.jar org.sonatype.mavenbook.App
Hello World!
```

因为这里我们成功的打了一个jar包，然后我们使用-cp命令把这个包加入到classpath中，然后我们的主类在org.sonatype.mavenbook中，就很自然的运行出来了

## 4. Simple Project Object Model

这一段对应3.4小结 [Simple Project Object Model](https://books.sonatype.com/mvnex-book/reference/simple-project-sect-pom.html)

'simple'的工程的POM文件很简单。仅仅描述了工程的artifactId，groupId和version。其实却是非常的简单，具体如下：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.sonatype.mavenbook.simple</groupId>
    <artifactId>simple</artifactId>
    <packaging>jar</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>simple</name>
    <url>http://maven.apache.org</url>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

比如在这里我们能看到maven的model的版本是4.0.0，也能看到我们的groupId还有artifactId，还有我们的artifact的version是1.0-SNAPSHOT，还能看到我们的打包类型是jar。

name和url都是一些描述性的元素，主要是给人类阅读的。

而且我们在dependencies这个元素下面看到了唯一一个依赖，值一个test scope的junit的依赖。

所以总的来看这个pom文件非常的简单。但是实际上这个pom文件包含的内容是非常的多的。

这里也提到了一个有趣的事情，就是不仅仅是我们眼前看到的这些属性是POM的属性，也有parent的属性，和Super-POM的属性，以及一些user-defined settings and active profiles.其实我没搞明白什么是active profile，后面继续看把。所有的这些parent POM, Super POM的属性都会充斥到我们的有效的POM张，叫做 effectiv-POM

所以想要看到所有生效的POM的属性，可以使用下面的命令：

```cmd
C:\Users\Q\Desktop\simple>mvn help:effective-pom
```

就可以看到所有的有效的POM的属性了

在我的机器上执行的时候看到的如下：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.sonatype.mavenbook</groupId>
  <artifactId>simple</artifactId>
  <version>1.0-SNAPSHOT</version>
  <name>simple</name>
  <url>http://maven.apache.org</url>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <repositories>
    <repository>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
      <id>central</id>
      <name>Central Repository</name>
      <url>https://repo.maven.apache.org/maven2</url>
    </repository>
  </repositories>
  <pluginRepositories>
    <pluginRepository>
      <releases>
        <updatePolicy>never</updatePolicy>
      </releases>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
      <id>central</id>
      <name>Central Repository</name>
      <url>https://repo.maven.apache.org/maven2</url>
    </pluginRepository>
  </pluginRepositories>
  <build>
    <sourceDirectory>C:\Users\Q\Desktop\simple\src\main\java</sourceDirectory>
    <scriptSourceDirectory>C:\Users\Q\Desktop\simple\src\main\scripts</scriptSourceDirectory>
    <testSourceDirectory>C:\Users\Q\Desktop\simple\src\test\java</testSourceDirectory>
    <outputDirectory>C:\Users\Q\Desktop\simple\target\classes</outputDirectory>
    <testOutputDirectory>C:\Users\Q\Desktop\simple\target\test-classes</testOutputDirectory>
    <resources>
      <resource>
        <directory>C:\Users\Q\Desktop\simple\src\main\resources</directory>
      </resource>
    </resources>
    <testResources>
      <testResource>
        <directory>C:\Users\Q\Desktop\simple\src\test\resources</directory>
      </testResource>
    </testResources>
    <directory>C:\Users\Q\Desktop\simple\target</directory>
    <finalName>simple-1.0-SNAPSHOT</finalName>
    <pluginManagement>
      <plugins>
        <plugin>
          <artifactId>maven-antrun-plugin</artifactId>
          <version>1.3</version>
        </plugin>
        <plugin>
          <artifactId>maven-assembly-plugin</artifactId>
          <version>2.2-beta-5</version>
        </plugin>
        <plugin>
          <artifactId>maven-dependency-plugin</artifactId>
          <version>2.8</version>
        </plugin>
        <plugin>
          <artifactId>maven-release-plugin</artifactId>
          <version>2.5.3</version>
        </plugin>
      </plugins>
    </pluginManagement>
    <plugins>
      <plugin>
        <artifactId>maven-clean-plugin</artifactId>
        <version>2.5</version>
        <executions>
          <execution>
            <id>default-clean</id>
            <phase>clean</phase>
            <goals>
              <goal>clean</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-resources-plugin</artifactId>
        <version>2.6</version>
        <executions>
          <execution>
            <id>default-testResources</id>
            <phase>process-test-resources</phase>
            <goals>
              <goal>testResources</goal>
            </goals>
          </execution>
          <execution>
            <id>default-resources</id>
            <phase>process-resources</phase>
            <goals>
              <goal>resources</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-jar-plugin</artifactId>
        <version>2.4</version>
        <executions>
          <execution>
            <id>default-jar</id>
            <phase>package</phase>
            <goals>
              <goal>jar</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <executions>
          <execution>
            <id>default-compile</id>
            <phase>compile</phase>
            <goals>
              <goal>compile</goal>
            </goals>
          </execution>
          <execution>
            <id>default-testCompile</id>
            <phase>test-compile</phase>
            <goals>
              <goal>testCompile</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.12.4</version>
        <executions>
          <execution>
            <id>default-test</id>
            <phase>test</phase>
            <goals>
              <goal>test</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-install-plugin</artifactId>
        <version>2.4</version>
        <executions>
          <execution>
            <id>default-install</id>
            <phase>install</phase>
            <goals>
              <goal>install</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-deploy-plugin</artifactId>
        <version>2.7</version>
        <executions>
          <execution>
            <id>default-deploy</id>
            <phase>deploy</phase>
            <goals>
              <goal>deploy</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-site-plugin</artifactId>
        <version>3.3</version>
        <executions>
          <execution>
            <id>default-site</id>
            <phase>site</phase>
            <goals>
              <goal>site</goal>
            </goals>
            <configuration>
              <outputDirectory>C:\Users\Q\Desktop\simple\target\site</outputDirectory>
              <reportPlugins>
                <reportPlugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-project-info-reports-plugin</artifactId>
                </reportPlugin>
              </reportPlugins>
            </configuration>
          </execution>
          <execution>
            <id>default-deploy</id>
            <phase>site-deploy</phase>
            <goals>
              <goal>deploy</goal>
            </goals>
            <configuration>
              <outputDirectory>C:\Users\Q\Desktop\simple\target\site</outputDirectory>
              <reportPlugins>
                <reportPlugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-project-info-reports-plugin</artifactId>
                </reportPlugin>
              </reportPlugins>
            </configuration>
          </execution>
        </executions>
        <configuration>
          <outputDirectory>C:\Users\Q\Desktop\simple\target\site</outputDirectory>
          <reportPlugins>
            <reportPlugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-project-info-reports-plugin</artifactId>
            </reportPlugin>
          </reportPlugins>
        </configuration>
      </plugin>
    </plugins>
  </build>
  <reporting>
    <outputDirectory>C:\Users\Q\Desktop\simple\target\site</outputDirectory>
  </reporting>
</project>
```
