---
title: "Advanced AMD Usage"
date: 2018-10-06
tag: dojo
excerpt_separator: <!--more-->
---

AMD的意思是异步模块定义（asynchronize module definition），是曾经一种比较流行的js模块定义方式
<!--more-->

## [Topic List 10] Advanced AMD Usage

### 1. this summarize some of the key point in Advanced AMD Usage

suppose we have a application with the following structure:

```cmd
/
    index.html
    js/
        lib/
            dojo/
            dijit/
            dojox/
        my/
        util/
```

### 2. How require function works

require function accept 3 parameters:

1. configuration (optional, default=undefined): an object with loader configuration options - this allows you to reconfigure the loader at run-time.

2. dependencies (optional, default=[]): an array of module identifiers. If specified, these modules will be resolved before your code is evaluated. They will be loaded in the order they are listed and passed as parameters to your callback function, also in order.

3. callback: a function containing the code you want to run that depends on the modules in dependencies. You need to wrap your code in a callback function in order to support asynchronous loading and to be able to use non-global references to the modules.

the following is a example of give a configuration object to *require* function:

```javascript
require(
    {
    baseUrl: "/js/",
        packages: [
            { name: "dojo", location: "//ajax.googleapis.com/ajax/libs/dojo/1.10.4/" },
            { name: "my", location: "my" }
        ]
    },
    ["my/app"]
);
```

here we change the baseUrl to */js/*, so the loader will go to /js/my/app to find the module named app, while module dojo will be find in the google CDN, as we described above.

### 3. How define works

define function accepts following parameters:

1. moduleId (optional, default=undefined): a module identifier. This parameter is largely a historical artifact of early AMD loaders or to support pre-AMD Dojo, and **should not be provided**.
2. dependencies (optional, default=[]): an array of module identifiers that are dependencies of your module. If specified, these modules will be resolved before your module is evaluated and they will be passed as parameters to your factory function, in order.
3. factory: the value of your module, or a "factory" function that will return the value

Usually we define a module with factory function to return a value which as the reference of the module. But define function allow us not provide the factory function.

following is a valid module:

```javascript
define({
    greeting: "Hello!",
    howAreYou: "How are you?"
});
```

Keep in mind that if you do define a module without using a factory function, you won’t be able to reference any dependencies, so this type of definition is rare and usually only gets used by i18n bundles or simple configuration objects.

### 4. How does the loader works

the basic idea is that: When you call *require* to load some modules, the loader has to find the code for the module and then pass it as a parameter to your callback function so you can use it.

1. First the loader has to resolve the module identifier you passed.
    - This involves putting together the baseUrl with the module identifier itself
    - plus taking into account any modifications required by other configuration options, such as map
2. At this point the loader has a URL for the module and can load the actual file by creating a new script element on the page and setting the src attribute to the module's URL. Actually create a **script tag** in the page.
3. Once the file is loaded and evaluated, its result is set as the value of the module.
4. The loader maintains a reference to each module, so the next time the module is requested the loader will return the existing reference.

### 5. Some details about the configuring the loader

1. baseUrl
2. tlmSiblingOfDojo
3. packages

when talking to using portable modules:

1. the possibility to use both old and current version of dojo, use map property.
2. and i find a cool thing that, that you can use require method inside the factory function send to define. This will use require function in the global scope.
3. if you want to use require in context-sensitive, as below:

```javascript
// in "my/debug.js"
define([
    "dojo/dom",
    "dojo/dom-construct",
    "dojo/on",
    "require"
], function(dom, domConstruct, on, require){
    on(dom.byId("debugButton"), "click", function(){
        require([ "./debug/console" ], function(console){
            domConstruct.place(console, document.body);
        });
    });
});
```

in the above code ,the require function will think it lives in the locally bound.

### 6. Handling circular dependencies

this chapter is complicated, i so skiped this chapter.

### 7. Non-AMD Code

As mentioned in the section on module identifiers, the AMD loader can also be used to load non-AMD code by passing an identifier that is actually a path to a JavaScript file. The loader identifies these special identifiers in **one of three ways**:
用三种之中的一种来鉴别是否是non-AMD模式的

1. The identifier starts with a “/”
2. The identifier starts with a protocol (e.g. “http:”, “https:”)
3. The identifier ends with “.js”

this sentence is important：

**When arbitrary code is loaded as a module, the module’s resolved value is undefined; you will need to directly access whatever code was defined globally by the script.**

so if i use /js/mylib/jQuery.js to load jQuery lib, it will live in the global scope, and i need to use it as $ or jQuery.

i think this is the most awesome feature for dojo.

### The Last: Lists of Topic to Read 

sadlly the todo list becomes longer... let us continue.

1. [1. Asynchronous Module Definition (AMD)](https://github.com/amdjs/amdjs-api/wiki/AMD)
2. [4. Query](https://dojotoolkit.org/documentation/tutorials/1.10/using_query/) and [Event](https://dojotoolkit.org/documentation/tutorials/1.10/events/)
3. [5. Ajax](https://dojotoolkit.org/documentation/tutorials/1.10/ajax/)
4. [6. Template-based Widgets](https://dojotoolkit.org/documentation/tutorials/1.10/templated/)
5. [7. Dijit Widget](https://dojotoolkit.org/documentation/?ver=1.10#widgets)
6. [8. Core Concept](https://dojotoolkit.org/documentation/?ver=1.10#coreConcepts)
7. [9. Feature Detection](http://)
8. [11. The Dojo Loader: More Details](https://dojotoolkit.org/reference-guide/1.10/loader/amd.html)
9. [12. Creating Classes use declare](https://dojotoolkit.org/documentation/tutorials/1.10/declare/)
