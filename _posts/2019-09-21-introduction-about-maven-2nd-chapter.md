---
title: "Introduction about Maven the 2nd chapter" 
date: 2019-09-21
tag: maven
---

The introduction about the maven the 2nd chapter
====


## 1. Some explanation about plugins

Plugins可不仅仅是减少maven distribution下载体积大小的一个小花招。Plugins为项目的构建（build）添加了新的行为。Maven从远程仓库中同时获取依赖和插件。书中讲了一个Surefire的插件，通过升级POM中的Surefire的版本，我们可以不修改其他的代码就升级单元测试的能力。

Maven针对所有的事情几乎都有对应的插件plugins，从编译java代码，到生成报告，到把应用部署到一个应用服务器，这些都有插件。Maven has abstracted common tasks into plugins which are maintained centrally and shared universally. This is what is meant by universal reuse through Maven Plugins.


## 2. Conceptual Model of a "Project"

Maven maintains a model of a project.我们做项目不仅仅是把java code编译成字节码，我们实际上是开发一个针对软件项目的描述，并且给出一系列唯一的坐标coordinates来表达的这个项目。那么通常来说maven的POM具有以下的特性：

1. Dependency Management：因为一个项目是通过一个唯一的坐标组来却认定的，这个坐标组包括group identifier， artifact id 和 version，所以利用这些东西项目可以声明自己的依赖。
2. Remote Repository：这一点与依赖管理相关，我们可以使用POM中定义的坐标来创建Maven Artifact的仓库。
3. Universal Resue of Build Logic：Plugins中包含了很多逻辑，这些逻辑可以根据POM文件中的描述性的数据或者是配置参数来工作。Plugins并不是被设计出来用于针对特定位置的文件来进行操作的。
4. Tool Portability/ Integration：自从有了POM的模型之后，像是eclipse，netbeans，IntelliJ现在就可以使用maven的POM文件来理解这个项目了。这就保证了项目在跨tools之间的通用一致性和可移植性。
5. Easy searching and Filtering of Project Artifacts：有了POM文件之后像是Nexus这样的工具就可以针对一个仓库进行项目的索引和检索了。

## 3. Install Maven on Microsoft Windows.

在windows上安装的时候，最简单的办法就是直接去Apache Maven上直接下载最新的稳定的包就可以了，安装其实也就是仅仅是下载和解压缩。在cmd中可以通过以下的方式来变更PATH：

```cmd
C:\Users\tobrien > set PATH="c:\Program Files\apache-maven-3.0.5\bin";%PATH%
```

使用这种方式的话，会在每一次的cmd的session中生效。

如果需要maven的PATH一直生效的话，就需要把Maven的bin目录加入到用户变量或者是环境变量中。

测试maven的安装：

```cmd
C:\Users\Q>mvn -v

Apache Maven 3.6.1 (d66c9c0b3152b2e69ee9bac180bb8fcc8e6af555; 2019-04-05T03:00:29+08:00)
Maven home: D:\mysdk\apache-maven-3.6.1\bin\..
Java version: 1.8.0_221, vendor: Oracle Corporation, runtime: D:\Java\jdk1.8.0_221\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

## 4. User-Specific Configuration and Repository
因为一些很常见的问题，比如我不想让我的windows的C盘很快的被占据满，比如我还有自己需要使用的私有的maven的仓库，所以基于这些原因我们肯定需要修改Maven的配置的。

Once you start using Maven extensively, you’ll notice that Maven has created some local user-specific configuration files and a local repository in your home directory. In ~/.m2 there will be:

只要我们稍微有点深度的开始使用maven，那我们就会注意到Maven创建了一些本地用户的配置文件和本地的仓库。都在~/.m2里面。

这里的波浪线~指的是用户的home文件夹。一般在linux里面，~指的是/home/username/

在windows 10里面呢，一般指的是 C:\Users\Q\.m2

下面是setting.xml文件的解释，还有repository就是本地的仓库的目录了。

~/.m2/settings.xml
A file containing user-specific configuration for authentication, repositories, and other information to customize the behavior of Maven.
~/.m2/repository/
This directory contains your local Maven repository. When you download a dependency from a remote Maven repository, Maven stores a copy of the dependency in your local repository.

我现在是直接修改了maven安装目录下面conf文件夹下面的setting.xml文件，这样是一种不好的实践。具体的原因如下：

Altering the global settings.xml file in the Maven installation itself is usually unnecessary and it tends to complicate the upgrade procedure for Maven as you’ll have to remember to copy the customized settings.xml from the old Maven installation to the new installation. 

## 5. Getting Help with Maven

http://maven.apache.org

You can browse the User mailing list archives at http://mail-archives.apache.org/mod_mbox/maven-users/. 

You can join the user mailing list by following the instructions available at http://maven.apache.org/mail-lists.html.

http://books.sonatype.com
Sonatype maintains an online copy of this book and other tutorials related to Apache Maven.