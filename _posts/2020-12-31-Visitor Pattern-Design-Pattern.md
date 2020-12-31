---
title: "The Visitor Pattern" 
date: 2020-12-31

tag: "Design Pattern"
---

在访问者模式（Visitor Pattern）中，我们使用了一个访问者类(Visitor)，它改变了元素类(Elements)的执行算法。通过这种方式，元素的执行算法(就是对于这个元素和其组成的访问和操作)可以随着访问者（Visitor改变而改变),不需要修改元素类本身。这种类型的设计模式属于行为型模式（Behavioral Pattern)。根据模式，元素对象接受(accept)访问者对象，这样访问者对象就可以针对元素对象进行操作。
<!--more-->

## 一、抽象类

在runoob上中找到了一篇不错的文档，[Visitor Pattern](https://www.runoob.com/design-pattern/visitor-pattern.html)

**目的**：主要将数据结构与数据操作分离。

**主要解决**：稳定的数据结构和易变的操作耦合问题。

**何时使用**：需要对一个对象结构中的对象进行很多不同的并且不相关的操作，而需要避免让这些操作"污染"这些对象的类，使用访问者模式将这些封装到类中。

**如何解决**：在被访问的类里面加一个对外提供接待访问者的接口（也就是accept方法和其实现）。

**关键代码**：在数据基础类里面有一个方法（accept）接受访问者，将自身引用（this）传入访问者。

**应用实例**：您在朋友家做客，您是访问者，朋友接受您的访问，您通过朋友的描述，然后对朋友的描述做出一个判断，这就是访问者模式。

**优点**： 1、符合单一职责原则。 2、优秀的扩展性。 3、灵活性。

**缺点**：

1. 具体元素对访问者公布细节，违反了迪米特原则。（因为针对不同类型的元素，Visitor的实现类要在Visit（Type a）中作出不同的访问实现）
2. 具体元素变更比较困难(没看出来哪里困难@todo)
3. 违反了依赖倒置原则，依赖了具体类，没有依赖抽象。（依赖倒置这个我理解，意思是针对不同类型的元素类，实现了不同的visit方法，所以是违反了依赖倒置吗？@todo）

**使用场景**：

1. 对象结构中对象对应的类很少改变，但经常需要在此对象结构上定义新的操作
2. 需要对一个对象结构中的对象进行很多不同的并且不相关的操作，而需要避免让这些操作"污染"这些对象的类，也不希望在增加新操作时修改这些类。

**注意事项**：访问者可以对功能进行统一，可以做报表、UI、拦截器与过滤器。

## 二、具体的例子

1. 我们将创建一个定义接受操作的 ComputerPart 接口。Keyboard、Mouse、Monitor 和 Computer 是实现了 ComputerPart 接口的实体类。
2. 我们将定义另一个接口 ComputerPartVisitor，它定义了访问者类的操作（因为定义了这个接口，所以后续其他种类的访问只需要实现这个接口就行了，这个接口本身不需要改动）。Computer使用访问者接口的实体类来执行相应的动作（比如在它的组成部分Component上，执行accept方法，并且在自己的accept方法中，执行访问者的visit方法，把自己的引用传递进去）。
3. VisitorPatternDemo，我们的演示类使用 Computer、ComputerPartVisitor 类来演示访问者模式的用法。

下面是具体的代码的例子了

```java
/*
 * ComputerPart.java
 */
public interface ComputerPart {
   public void accept(ComputerPartVisitor computerPartVisitor);
}
```

这个接口定义了computer及其组成部分与visitor之间的统一的contract合同/合约，也就是以接口的形式体现。那么所有的组成部分都要实现这个接口，下面有键盘，显示器，鼠标和整机的4个元素类，都实现了具体上面的接口

```java
/*
 * KeyBoard.java
 */
public class Keyboard implements ComputerPart {
   @Override
   public void accept(ComputerPartVisitor computerPartVisitor) {
      computerPartVisitor.visit(this);
   }
}
```

```java
/*
 * Monitor.java
 */
public class Monitor  implements ComputerPart {
   @Override
   public void accept(ComputerPartVisitor computerPartVisitor) {
      computerPartVisitor.visit(this);
   }
}
```

```java
/*
 * Mouse.java
 */
public class Mouse  implements ComputerPart {
   @Override
   public void accept(ComputerPartVisitor computerPartVisitor) {
      computerPartVisitor.visit(this);
   }
}
```

```java
/*
 * Mouse.java
 */
public class Mouse  implements ComputerPart {
   @Override
   public void accept(ComputerPartVisitor computerPartVisitor) {
      computerPartVisitor.visit(this);
   }
}
```

```java
/*
 * Computer.java
 */
public class Computer implements ComputerPart {
   
   ComputerPart[] parts;
 
   public Computer(){
      parts = new ComputerPart[] {new Mouse(), new Keyboard(), new Monitor()};      
   } 
 
 
   @Override
   public void accept(ComputerPartVisitor computerPartVisitor) {
      for (int i = 0; i < parts.length; i++) {
         parts[i].accept(computerPartVisitor);
      }
      computerPartVisitor.visit(this);
   }
}
```

到这里我们还是要罗嗦一下的，看到键盘的accept方法其实很简单的，就是在accept方法中，直接调用对应的visitor的visit方法，然后把自己作为一个元素的reference（this）传递进去就形了，因为键盘在Element构成的结构树（element hierarchy)中已经是末端的元素了，所以它的accept方法非常简单。

然而当我们看到电脑（Computer.java）的accept方法的时候，我们发现就复杂很多了，因为Computer不是元素结构树上的一个末端的元素，它自己本身还包含了下层的元素，所以这种情况下，就需要在这类中间节点的accept方法中，指明应该怎么访问其下属元素节点和它本身。所以看到Computer类中的accept方法，就像一个二叉树的后序遍历一样。但是整体来看代码还是非常简单的。

好了，既然已经定义好了，元素要能接纳访问者Visitor对象，然后Visitor对象要访问元素，所以下面就开始定义Visitor类型的接口了，其实就是针对不同的元素定义visit方法，然后针对这个接口，写出不同的实现。

```java
/*
 * ComputerPartVisitor.java
 */
public interface ComputerPartVisitor {
   public void visit(Computer computer);
   public void visit(Mouse mouse);
   public void visit(Keyboard keyboard);
   public void visit(Monitor monitor);
}
```

从上面的接口，我们可以看出来，其实就是针对不同的Element来定义了访问者针对不同元素的visit接口。也就是文章开始说的第三个缺点，interface依赖了具体的类，而不是依赖了一个抽象的接口。

下面就是具体的一个visitor的实现类了

```java
/*
 * ComputerPartDisplayVisitor.java
 */
public class ComputerPartDisplayVisitor implements ComputerPartVisitor {
   @Override
   public void visit(Computer computer) {
      System.out.println("Displaying Computer.");
   }
   @Override
   public void visit(Mouse mouse) {
      System.out.println("Displaying Mouse.");
   }
   @Override
   public void visit(Keyboard keyboard) {
      System.out.println("Displaying Keyboard.");
   }
   @Override
   public void visit(Monitor monitor) {
      System.out.println("Displaying Monitor.");
   }
}
```

下面的代码就是在Design Pattern中一个具体的client怎么使用Visitor来访问具体的元素了。

```java
/**
 * VisitorPatternDemo.java
 */
public class VisitorPatternDemo {
   public static void main(String[] args) {
 
      ComputerPart computer = new Computer();
      computer.accept(new ComputerPartDisplayVisitor());
   }
}
```

从上面的代码中，我们确实可以很清楚的看出来，元素的结构（Composite Pattern）确实和针对元素的操作行为（Visitor）已经分离到2个类中了，代码上非常的漂亮，很简洁，而且清晰。

但是确实就是文中开始所说的问题，因为是2个不同的类，作为Visitor来说确实知道了Element的结构了，因为有几种类型，这种信息都暴露出来了，就不完全是一个黑盒子了，所以后续如果Element Hierarchy变化了，那对应的Visitor要改，所有其实现类，也都是需要修改的，所以这个是一个大问题。

所以文章的开头就说了，适合Element结构非常稳定，但是针对Element的行为变化很大的场景。

![Visitor Pattern Classes Diagram](https://www.runoob.com/wp-content/uploads/2014/08/visitor_pattern_uml_diagram.jpg)