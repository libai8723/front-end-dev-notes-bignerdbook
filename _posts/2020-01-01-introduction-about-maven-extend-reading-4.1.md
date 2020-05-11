---
title: "Introduction about Maven extending reading on Maven Official Website" 
date: 2020-05-11
tag: maven
---


## 1. Introduction to the Build Lifecycle

这篇文档是Maven 官方网站的介绍，感觉还是不错的，就当作是扩展的阅读吧。[Introduction to the Build Lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)

### 1.1 Build Lifecycle Basics

Maven is based around the central concept of a build lifecycle. What this means is that the process for building and distributing a particular artifact (project) is clearly defined.

Maven是构建在build lifecycle这个核心的概念上的。这句话的意思是说，对于使用Maven来构建（building）和分发（distributing）一个特定的artifact来说，这个过程是被清晰定义的。

For the person building a project, this means that it is only necessary to learn a small set of commands to build any Maven project, and the POM will ensure they get the results they desired.

而对于一个想要构建这个工程的人来说，这意味着这个人只需要学习一小部分构建的命令就可以了，POM [Intro to POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) 就会保证他能得到他想要的结果。

There are three built-in build lifecycles: default, clean and site. The default lifecycle handles your project deployment, the clean lifecycle handles project cleaning, while the site lifecycle handles the creation of your project's site documentation.

总的来说，一共有三种内置（built-in）的生命周期（Lifecycle），也就是：

1. default: default声明周期主要处理项目的部署（deployment），这里的部署，应该是广义的部署，就包含了构建，测试，等等。
2. clean: clean生命周期主要完成项目的清理。
3. site: site声明周期主要完成项目的site文档的生成。

### 1.2 Build Lifecycle is Made Up of Phases

Each of these build life cycles is defined by a different list of build phases, wherein a build phase represents a stage in the lifecycle.

上面提到的build life cycles，它们每一个都是被一系列不同的build phases（构建阶段）所定义的，而每一个构建阶段都代表了在lifecycle中的一个阶段。

For example, the default lifecycle comprises of the following phases (for a complete list of the lifecycle phases, refer to the Lifecycle Reference):

例如，默认生命周期（default lifecycle）是由下面的一系列的phases组装成的（如果想要知道一份完整的lifecycle phases的列表，可以参照这里[Life Cycle Reference](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference)）

下面是default lifecycle中常见的build phases的名字和解释：

* validate - validate the project is correct and all necessary information is available
* compile - compile the source code of the project
* test - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
* package - take the compiled code and package it in its distributable format, such as a JAR.
* verify - run any checks on results of integration tests to ensure quality criteria are met
* install - install the package into the local repository, for use as a dependency in other projects locally
* deploy - done in the build environment, copies the final package to the remote repository for sharing with other developers and projects.

下面做简要的说明：

* validate - 验证项目的正确性，并且确保所有需要的信息都是可以找到的。
* compile - 编译项目的源代码
* test - 测试项目的源代码，使用一些适合的单元测试的框架。通常来说，这些tests不应该需要代码被打包或者部署
* package - 把编译后的代码按照可以分发的格式打包起来，例如一个jar文件
* verify - 这个环节其实是做集成测试的，也就是说可以在这个环节完成部署后的测试，来确保质量要求达标
* install - 把构建出来的包安装到本地仓库中，从而可以在其他项目中使用这个依赖
* deploy - 在开发环境中完成上述的所有事情之后，把最终的产物package拷贝到远程的仓库中，从而可以让其他的项目和开发者来共享。


These lifecycle phases (plus the other lifecycle phases not shown here) are executed sequentially to complete the default lifecycle. Given the lifecycle phases above, this means that when the default lifecycle is used, Maven will first validate the project, then will try to compile the sources, run those against the tests, package the binaries (e.g. jar), run integration tests against that package, verify the integration tests, install the verified package to the local repository, then deploy the installed package to a remote repository.

上面提到的生命周期的阶段（当然还有其他那些属于default lifecycle但是没有列举在这里的build phases）都会按照顺序执行，从而完成一个完整的default lifecycle。对于我们上面列举的lifecycle phases来说，这个意味着，Maven首先完成项目的验证，然后编译源代码，执行单元测试，打包文件，在打包的文件上执行集成测试并且验证集成测试的结果，安装打包文件到本地仓库，最终部署安装包到远程仓库中。

### 1.3 Usual Command Line Calls

You should select the phase that matches your outcome. If you want your jar, run package. If you want to run the unit tests, run test.

通常来说，你应该按照你的输出来选择你需要的phases，比如你想要打包，那就用package，如果你想要执行单元测试，那就是运行test

If you are uncertain what you want, the preferred phase to call is

如果你也不确定你要什么，那么你一般使用verify比较好

```cmd
mvn verify
```

@todo,看到这里了 [Usual Command Line](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#usual-command-line-calls)