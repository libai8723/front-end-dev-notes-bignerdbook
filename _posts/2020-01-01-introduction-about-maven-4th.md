---
title: "Introduction about Maven the 4th chapter" 
date: 2020-01-01
tag: maven
---


## 1. Core Concepts

现在我们已经成功的第一次运行maven了，那么现在是时候对maven的一些基本概念做出讲解了。

那么让我们回想以下在之前的例子中，我们都干了什么呢：

1. 我们使用Maven的archetype来生成了一个hello world工程
2. 这个工程创建好了之后，其实是由一个POM文件和一些java代码，按照典型的Maven项目目录结构来构成的。
3. 然后我们执行了mvn install，这里把这个动作叫做我们执行了一个lifecycle phase as an argument，这个东西提示
4. Maven去执行一系列的Maven插件的goals，
5. 最终我们的Maven Artifact被安装到了本地仓库local repository

是不是有点晕，什么是lifecycle，什么是goals，什么是local repository？那么本章主要是讲这些的。来自于 [3.5 Core Concepts](https://books.sonatype.com/mvnex-book/reference/simple-project-sect-simple-core.html)

## 1.1 Maven Plugins and Goals

为了执行一个Maven Plugin的Goal，我们可以使用这样的语法：

```cmd
$mvn archetype:generate
...
[INFO] [archetype:generate]
...
```

这里的archetype是一个插件（plugin）的标识符，而这里的generate是一个goal的标识符。

当Maven执行一个插件的Goal的时候，它会把插件的标识符和目标的标识符打印到标准输出中，就像上面这样。

### 1.1.1 What is a Plugin

一个Maven的插件就是一个或者几个goals的集合。

A maven plugin is a collection of one or many goals.

![A Plugin contains Goals Image](/front-end-dev-notes-bignerdbook/assets/img/PluginContainsGoals.png)

那么我们来看几个比较简单的但是核心的插件的例子：

1. **Jar plugin**: which contains goals for creating JAR files.
2. **Compiler plugin**: which contains goals for compiling source code and unit tests.
3. **Surefire plugin**: which contains goals for executing unit tests and generating report.

上面说的都是非常常见和通用的plugins，那么当然还有一些非常特殊的plugins，例如：

1. **Hibernate3 plugin**: for integration with the popular persistence library Hibernate.
2. **JRuby plugin**: which allows you to execute Ruby as part of Maven build or to write Maven plugins in Ruby
3. **customer plugins**: Maven also provides for the ability to define custom plugins. A custom plugin can be written in Java.

### 1.1.2 What is a Goal

1. A goal is a specific task that may be executed as a standalone goal or along with other goals as part of a larger build. Goal是一些特定的任务，这些goal可以被单独执行，或者与其他goal一起作为一个大型build过程的一部分。
2. A goal is a "Unit of Work" in Maven. Goal在Maven中是工作的单位。

下面是一些Goal的样例：

1. **compile** goal in the Compiler plugin
2. **test** goal in the Surefire plugin

Goals 可以通过一些配置属性来进行配置，从而customize behavior。关于customize behavior的事情，其实我们已经看过一个例子了，那就是之前我们使用archetype:generate来生成了一个简单的simple project，我们指定了package属性。

```text
When referring to a plugin goal, we frequently use the shorthand notation: pluginId:goalId.
For example, when referring to the generate goal in the Archetype plugin, we write archetype:generate.
注意这里的archetype:generate是一个goal的简写
```

### 1.1.3 Sensible Default Values

Goals define parameters that can define sensible default values. Goals定义了参数，并且这些参数是可以定义有意义的默认值的。这其实进一步说明了 convention over configuration的设计思想。

在archetype:generate中我们指定了groupId和artifactId，但是我们没有指定type of artifact，所以generate这个goal会在指定的过程中，停下来，给我们一个prompt，来问我们想要创建什么样子的artifact。

反过来如果我们使用archetype:create的goal的话，那么maven会假定我们希望产生一个maven-archetype-quickstart类型的的archetype。这就是Sensible default value的很好的例子。

```text
但是我们从实际的实验来看，如果直接执行archetype:generate这个gold的话，会报错的，因为最新版本的archetype插件已经移除了create这个goal，只能使用generate这个goal了
```

这里作者又强调了以下Maven的设计的思想和原理：

The core of Maven has little to do with the specific tasks involved in your project's build. By itself, Maven doesn't know how to compile your code or even how to make a JAR file. It delegates all of these work to Maven plugins like the Compiler plugin and the Jar Plugin.

## 1.2 Maven lifecycle

在生成simple这个工程的之后，我们使用了第二个命令，这个命令是：

```cmd
$mvn install
```

这个命令以检查工程的正确性作为起始，然后以部署工程到生产作为终止。 Lifecycle Phases是故意被定义的含糊不清，或者说是泛化的，主要被定义为验证（validation），测试（testing），部署（deployment），对于不同类型的工程而言它们具有不同的含义。例如对于一个要产生Jar文件的工程来说，package phase会产生一个JAR文件；而对于一个web application来说package phase会产生一个WAR文件。

Plugin Goals can to attached to a lifecycle phase.

注意这里说的是插件的目标，是可以被隶属于某一个生命周期的阶段的。

As Maven moves through the phases in a lifecycle, it will execute the goals attached to each particular phase.

随着Maven经过不同的生命周期的阶段的时候，Maven会执行隶属于这些生命周期阶段的目标。每一个生命周期的阶段，都可以有0个或者多个目标。

在我们之前执行mvn install的死后，我们注意到不止一个目标被执行了。我们知道在package阶段，我们使用jar插件生成了一个jar文件，那么在package阶段之前，Maven做了什么呢？下面是详细的列表了：

1. resources:resources

    plugin is bound to the process-resources phase. This goal copies all of the resources from src/main/resources and any other configured resource directories to the output directory.

2. compiler:compile

    is bound to the compile phase. This goal compiles all of the source code from src/main/java or any other configured source directories to the output directory.

3. resources:testResources

    plugin is bound to the process-test-resources phase. This goal copies all of the resources from src/test/resources and any other configured test resource directories to a test output directory.

4. compiler:testCompile

    plugin is bound to the test-compile phase. This goal compiles test cases from src/test/java and any other configured test source directories to a test output directory.

5. surefire:test

    bound to the test phase. This goal executes all of the tests and creates output files that capture detailed results. By default, this goal will terminate a build if there is a test failure.
6. jar:jar

    to the package phase. This goal packages the output directory into a JAR file.

下面是一张图，来说明生命周期各阶段和我们的插件的goal之间的关系：
![Image of LifeCycle Phases and Plugins' Goals](https://books.sonatype.com/mvnex-book/reference/figs/web/simple-project_lifecyclebinding.png)

需要注意的是，图中也说明白了，这些图中的生命周期的阶段仅仅是一部分，并不是全集。如果需要找到全部的lifecycle phases，可以参照[Reference](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference)

那么让我们来总结一下，当我们执行mvn install的时候，Maven会执行所有在上图中在install phase上方的phase，所以在执行这些lifecycle phase的时候，依附在这些phase上的goal就会被执行，也就是上图中右侧的圆角方框。如果你不想执行一个maven lifecycle goal，那么我们可以顺序的执行下面的插件的goal，这两者是等价的。

```cmd
$ mvn resources:resources \
    compiler:compile \
    resources:testResources \
    compiler:testCompile \
    surefire:test \
    jar:jar \
    install:install
```

那么我们从命令的复杂程度可以看出，执行maven的lifecycle phase比在命令行中显式的执行plugin goal要简单很多。更重要的是，the common lifecycle允许每一个项目都适配了Maven预先定义好的一系列标准。

The lifecycle is what allows a developer to jump from one Maven project to another without having to know very much about the details of each particular project’s build. If you can build one Maven project, you can build them all.

## 1.3 Maven Coordinates

首先还是解释一下什么是pom文件，我们在前面的过程中使用mvn archetype:generate来生成了一个项目，在项目的根目录中有一个文件叫做pom.xml，这个文件就是Project Object Model，一个关于项目的声明式的描述文件。

When maven executes a goal, each goal has access to the information defined in a project's POM. Goals execute in the context of POM.

Goals are actions we wish to take upon a project, and a project is defined by a POM.

POM命名了一个项目，针对一个项目提供了一组唯一的标识符（coordinates）。并且POM定义了我们当前的项目的它的一些东西的关系，其他的一些东西指的是依赖库（dependencies），父项目（parents）和先决条件（prerequisite）。

同样的我们也可以通过POM文件来定制化Plugin的行为，提供这个项目的关于关于社区和参与其中的开发人员的信息。

下面的图是一个典型的POM的样例:

![some example about POM](https://books.sonatype.com/mvnex-book/reference/figs/web/simple-project_annopom.png)

在这个样例中，我们标红的部分，我们就说是这个项目的coordinates，这里可以看到有4个element构成了我们的项目的coordinates，分别是groupId，artifactId，packaging，version。事实上还有第五个元素，叫做 classifier，目前来说我们不太关心这个classifier是干啥的，在本书的后续的部分我们会介绍这个。@todo

下面的句子写非常经典，就不做翻译了：

Just like any other coordinate system, a set of Maven coordinates is an address for a specific point in "space".

Maven pinpoints a project via its coordinates when one project relates to another, either as a dependency, a plugin, or a parent project reference.

Maven coordinates are often written using a colon as a delimiter in the following format:

```text
groupId:artifactId:packaging:version
```

注：space这个词，在数学中有非常强的抽象的意义，比如希尔伯特空间，自然数空间，实数空间，有理数空间等等；基于此还能衍生出比如有理数空间是否是致密的等等。空间其实一个抽象的概念，有点像集合的概念。说不太清楚，因为在xml，在python，等等原文介绍中经常还会出现namespace这种名词。为什么要出现空间，一者是为了界定什么是合法的，二者是为了区分。

## 1.4 Maven Repository

其实当我们第一次运行mvn archetype:generate的时候，我们就会注意到，我们下载了很多东西的。因为maven就是这么设计的。

Maven ships with the bare minimum and fetches from a remote repository when it needs to. Maven ships with a default remote repository location (<http://repo1.maven.org/maven2)> which it uses to download the core Maven plugins and dependencies.

Often you will be writing a project which depends on libraries that are neither free nor publicly distributed. In this case you will need to either setup a custom repository inside your organization’s network or download and install the dependencies manually.

What makes a Maven repository a Maven repository? A repository is a collection of project artifacts stored in a directory structure that closely matches a project’s Maven coordinates. You can see this structure by opening up a web browser and browsing the central Maven repository at <http://repo1.maven.org/maven2/.> You will see that an artifact with the coordinates org.apache.commons:commons-email:1.1 is available under the directory /org/apache/commons/commons-email/1.1/ in a file named commons-email-1.1.jar. The standard for a Maven repository is to store an artifact in the following directory relative to the root of the repository:

```text
/<groupId>/<artifactId>/<version>/<artifactId>-<version>.<packaging>
```

Maven installed our project’s JAR file into our local Maven repository. Maven uses the local repository to share dependencies across local projects. If you develop two projects—project A and project B—with project B depending on the artifact produced by project A, Maven will retrieve project A’s artifact from your local repository when it is building project B. Maven repositories are both a local cache of artifacts downloaded from a remote repository and a mechanism for allowing your projects to depend on each other.

## 1.5 Maven's Dependency Management(@todo, pending reading
