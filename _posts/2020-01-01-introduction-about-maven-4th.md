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

### 1.1.1 What is a Plugin?

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

### 1.1.2 What is a Goal?

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

