---
title: "The Local Classes" 
date: 2021-01-22

tag: "JLS&JVM Spec"
---

继续讨论local classes的定义和特性，以及与内部类的相似之处

<!--more-->

## 一、本地类 Local Class

本地类是定义在一段代码块中的，所谓代码快就是2个大括号包围的一组语句，我们通常是在一段函数中可以发现本地类的定义。

本章节主要讨论下面的几个话题

1. 声明本地类

2. 访问包含本地类的Enclosing类的成员
    1. 遮盖

3. 本地类和内部类是比较相似的

## 二、声明一个本地类

下面这个 LocalClassExample 的类，验证2个电话号码，它在一个叫做alidatePhoneNumber的函数中定了一个本地类PhoneNumber

```java
package LocalClasses;

public class LocalClassExample {
    static String regularExpression = "[^0-9]";

    public static void validatePhoneNumber(
            String phoneNumber1, String phoneNumber2) {

        final int numberLength = 10;

        // Valid in JDK 8 and later:
        // int numberLength = 10;

        class PhoneNumber {

            String formattedPhoneNumber = null;

            PhoneNumber(String phoneNumber) {
                // numberLength = 7;
                String currentNumber = phoneNumber.replaceAll(
                        regularExpression, "");
                if (currentNumber.length() == numberLength)
                    formattedPhoneNumber = currentNumber;
                else
                    formattedPhoneNumber = null;
            }

            public String getNumber() {
                return formattedPhoneNumber;
            }

            // Valid in JDK 8 and later:
            public void printOriginalNumbers() {
                System.out.println("Original numbers are " + phoneNumber1 +
                    " and " + phoneNumber2);
            }
        }

        PhoneNumber myNumber1 = new PhoneNumber(phoneNumber1);
        PhoneNumber myNumber2 = new PhoneNumber(phoneNumber2);

        // Valid in JDK 8 and later:
        myNumber1.printOriginalNumbers();

        if (myNumber1.getNumber() == null)
            System.out.println("First number is invalid");
        else
            System.out.println("First number is " + myNumber1.getNumber());
        if (myNumber2.getNumber() == null)
            System.out.println("Second number is invalid");
        else
            System.out.println("Second number is " + myNumber2.getNumber());

    }

    public static void main(String... args) {
        validatePhoneNumber("123-456-7890", "456-7890");
    }
}
```

从这段代码来说，也没有特别的，就是在一个静态类函数里面声明了一个PhoneNumber的类，然后这个类的构造函数在执行的时候，会把非数字的字符都替换掉，并且检查替换之后的电话号码的长度是10，都满足条件的话，就放在foramttedPhoneNumber的实例变量中。所以输出也非常简单阿，就是下面这两行：

First number is 1234567890
Second number is invalid

从这段代码来看平淡无奇呀。

## 三、访问外部包含类的成员（Accessing Members of an Enclosing Class）

本地类可以访问包含它的外部的类的成员，在前面的例子中，PhoneNumber的构造器访问了LocalClassExample.regularExpression的静态类变量。

另外，本地类也可以访问局部的变量。然而本地类只能访问被声明为final的本地变量。

当一个本地类访问一个包含它的块（Scope）的局部变量或者参数的时候，它（local classes）就捕获了这个变量或者参数，我们把这个种情况叫作 **captured variable or captured parameter**

```text
从Java8开始，本地类可以访问local variable和parameter了，只要他们是final或者是 effectively final。后者的意思是，只要这个变量在初始化之后，在也没有发生过变化，那么它就是一个 effectively final

所以可以仔细思考一下，上面那个被注释掉的代码  // numberLength = 7; 这行代码就是在变量初始化之后，又修改了变量，就不属于 effectively final
```

关于在method中声明一个类：

```text
从Java8开始，可以在method中声明类了。但是我试验了一下，在method中声明的类，也就只能在在声明这个类的方法中使用了。

比如我想在一个类静态方法中声明一个类，当然是可以的，但是出了这个静态方法，就访问不到这个类了。
```

@todo,看到了 Local Classes Are Similar To Inner Classes，这个章节了

[Local Classes Are Similar To Inner Classes](https://docs.oracle.com/javase/tutorial/java/javaOO/localclasses.html)
