---
title: "Introduction to AMD Module"
date: 2018-10-05
tag: Mendix, Dojo, AMD， JavaScript
---

## [Topic List 2 ]Introduction to AMD Module

### 1. After reading hello dojo, Modern dojo, dojoCondig

It seem I have a much deeper understanding about the Module, but the first exmaple code seem pretty cool, so I keep it down here

### 2. What is a Module

- A module is a value that can be accessed by a single reference.
- If you have multiple pieces of data or functions that you want to expose in a module, they have to be properties on a single object that represents the module.
- A module is stored in your file system in a single file.

### 3. How to Create a Module

- With AMD, you create a module by registering it with the loader.
- A loader is the code that handles the logic behind defining and loading modules.

### 4. A simple module example

```javascript
define(function(){
    var privateValue = 0;
    return {
        increment: function(){
            privateValue++;
        },

        decrement: function(){
            privateValue--;
        },

        getValue: function(){
            return privateValue;
        }
    };
});
```

the basic idea is that define an anonymous function in define function, and return a object to expose the functionality.

and this module will be contains in a javascript file, when you want to use it, use require to include it. require will load the file and pass the object as a parameter to the anonymous function in require function.

so below example show how to use a module:

```javascript
<html>
    <body>
        <script src="dojo/dojo.js" data-dojo-config="async: true"></script>
        <script>
            require([
                "app/counter"
            ], function(counter){
                log(counter.getValue());
                counter.increment();
                log(counter.getValue());
                counter.decrement();
                log(counter.getValue());
            });
        </script>
    </body>
</html>
```

### 5. Modules load Modules

use this example code below:

```javascript
define([
    "dojo/_base/declare",
    "dojo/dom",
    "app/dateFormatter"
], function(declare, dom, dateFormatter){
    return declare(null, {
        showDate: function(id, date){
            dom.byId(id).innerHTML = dateFormatter.format(date);
        }
    });
});
```

1. in this example we show multiple dependencies, so you can see multiple modules list in the dependency list.
2. and this module return a constructor, which i do not know, maybe defined by declard function. So definitely we should go through function *declare*

### 6. Using plguins

In addition to regular modules, the AMD loader also features a new type of module called a plugin. Plugins are used to extend the loader with new features beyond simply loading an AMD module. Plugins are loaded more or less the same way as a regular module, but use a special character "!" at the end of the module identifier to identify the request as a plugin request. Data after the "!" is passed directly to the plugin for processing.

1. dojo/text: will load a string from a file
2. dojo/i18n: loads language resource bundles according to the web browser's user locale. Its usage looks like this:
3. dojo/has: Dojo’s loader includes an implementation of the has.js feature detection API; the dojo/has plugin leverages this functionality for requiring modules conditionally.
4. dojo/domReady: It is a module that simply doesn’t resolve until the DOM is ready.

### The Last: Lists of Topic to Read

sadlly the todo list becomes longer... let us continue.

1. [1. Asynchronous Module Definition (AMD)](https://github.com/amdjs/amdjs-api/wiki/AMD)
2. [4. Query](https://dojotoolkit.org/documentation/tutorials/1.10/using_query/) and [Event](https://dojotoolkit.org/documentation/tutorials/1.10/events/)
3. [5. Ajax](https://dojotoolkit.org/documentation/tutorials/1.10/ajax/)
4. [6. Template-based Widgets](https://dojotoolkit.org/documentation/tutorials/1.10/templated/)
5. [7. Dijit Widget](https://dojotoolkit.org/documentation/?ver=1.10#widgets)
6. [8. Core Concept](https://dojotoolkit.org/documentation/?ver=1.10#coreConcepts)
7. [9. Feature Detection](http://)
8. [10. Advanced AMD Usage tutorial](https://dojotoolkit.org/documentation/tutorials/1.10/modules_advanced/)
9. [11. The Dojo Loader: More Details](https://dojotoolkit.org/reference-guide/1.10/loader/amd.html)
10. [12. Creating Classes use declare](https://dojotoolkit.org/documentation/tutorials/1.10/declare/)
