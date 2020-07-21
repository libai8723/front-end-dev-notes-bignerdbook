---
title: "Introduction about Maven the 1st chapter" 
date: 2019-09-18
tag: maven
---

## The introduction about the maven the 1st chapter

### 1. What is Maven

The answer to this question depends on your perspective.
大部分人可能认为maven是一个build tool，也就是构建的工具——a tool used for build deployable artifacts from source code.
for build engineers and project managers might refer to Maven as something more comprehensive: a project management tool.

The following is the formal definition of Apache Maven:

1. a project management tool which emcompasses a project model.
2. a set of standards.
3. a project lifecycle.
4. a dependency management systems.
5. logic for executing plugin goals at defined phases in a lifecycle.

### 2. Convention over configuration

标题是非常有意思的。个人在没有看这个文章的介绍的时候，认为这个语法类似于敏捷宣言的写法。

Before reading this chapter [Convention Over Configuration](https://books.sonatype.com/mvnex-book/reference/installation-sect-conventionConfiguration.html), I think the title of this chapter are using the pattern that used in the [Agile-Manifesto](https://agilemanifesto.org/), so i think maven prefer Convention rather than writing every configuration explicityly.

事实证明我的理解是正确的，如果不做显式的配置的话，那么下面这些东西都是默认的值：

1. source code is assumed to be in **${basedir}/src/main/java**
2. resources are assumed to be in **${basedir}/src/main/resources**
3. Tests are assumed to be in **${basedir}/src/test**
4. a project is assumed to produce a JAR file.
5. Maven assumes that you want the compile bytecode to **${basedir}/target/classes**
6. Distributable JAR file in **${basedir}/target**.

Maven对于“Convention Over Configuration”观念的采用，远远不仅是定义源代码的位置在哪里。Maven的核心插件使用一系列约定俗成的约定来完成：编译源码，打包分发，生成web sites和很多其他的处理工作。

Maven's strength comes from the fact that it is "Opinionated".之所以说Maven是带有很多偏见和陈规的，是因为它有约定好的lifecycle，还有很多通用的plugins。

### 3. A Common Interface

在Maven之前，比如2001年的时候，被称为：Age of "Build Engineer"，简直就是黑暗时代，因为大家都有各种各样不同的软件构建的方案，非常困难。

在Maven之后，大家只需要干一件事情，那就是：These days, you check it out from source, and you run **mvn install**.

看来npm install不是它自己发明的，我还以为是npm的发明呢，这么方便，原来这个轮子是Java的。

所以，这个非常碉堡啊。就像node的project，我们只要认为它是一个合法的nodejs的project，大概率我只需要npm install，我就能搞定所有的依赖，并且npm start开始运行了。
