---
title: "Modern Dojo"
date: 2018-10-04
tag: Dojo
excerpt_separator: <!--more-->
---

这其实是一章总览，介绍现代化（2013年左右吧）的Dojo的开发和一些基本的概念，当时我看了2天，觉得真心不错。后来为什么被三大框架取代了呢。
<!--more-->

## [Topic List 3] Modern Dojo

### 1. Background

So as the saying in [dojo framework](https://dojotoolkit.org), modern dojo is much different with dojo version before 1.6

So this topic talk about the modern dojo thinking and difference.

### 2. Core Concept: things in the global namespace are bad

1. the global namespace can easily become polluted with all manner of code

### 3. synchronous operations are slow and asynchronous ones are usually faster

1. so dojo AMD use asynchronous way to load modules

### 4. Baseless

1. You might hear the term "baseless" when dealing with "modern" Dojo. This is talking about ensuring that a module doesn't depend on any more of the "base" Dojo functionality than it needs.

### 5. Event and Advice

1. Dojo using dojo/on for event handling and dojo/aspect for method advice

2. While advice is a concept that is part of aspect oriented programming (AOP) that add additional behavior to a join point (or method). While many parts of Dojo mostly followed AOP the dojo/aspect module provides a centralized mechanism to manifest this.

3. so Advice basiclly talks about AOP

### 6. Topics

1. Not sure about this, maybe deal with publish and subscribe things

### 7. Promises

1. totally not understand about promises and its usages.

### 8. Request

1. More on Ajax

### 9. DOM Manipulation

1. not only has Dojo abandoned its dependency on the global namespace and adopted some new patterns
2. it has also broken out some of "core" functionality into modules and what is more core to a JavaScript toolkit

### 10. Data Store

### 11. Dijit and Widgets

1. This is what i need to go through carefully.

### 12. Parser

1. Finally, there is the dojo/parser. Dojo has had strength in both a programmatic and declarative markup way, with the dojo/parser handling the interpretation of the declarative markup and converting that into instantiated objects and widgets.

2. know a basic concept, now do have to go through carefully.

### 13. Builder

1. you can build you own tailed js lib for deployment, so the builder in powerful.

### The Last: Lists of Topic to Read

1. [Asynchronous Module Definition (AMD)](https://github.com/amdjs/amdjs-api/wiki/AMD)
2. [Dojo's AMD tutorial](https://dojotoolkit.org/documentation/tutorials/1.10/modules)
3. [Query](https://dojotoolkit.org/documentation/tutorials/1.10/using_query/) and [Event](https://dojotoolkit.org/documentation/tutorials/1.10/events/)
4. [Ajax](https://dojotoolkit.org/documentation/tutorials/1.10/ajax/)
5. [Template-based Widgets](https://dojotoolkit.org/documentation/tutorials/1.10/templated/)
6. [Dijit Widget](https://dojotoolkit.org/documentation/?ver=1.10#widgets)
7. [Core Concept](https://dojotoolkit.org/documentation/?ver=1.10#coreConcepts)
