Learn Dojo Hello Course
===

## 1. Background

For the last 3 days, i was trying to build a mendix widgets that display resource schedule, of course i do not want to build it from scratch. So i used fullcalendar, but sadlly i can not handle the 4 modules 'moment, jQuery, fullcalendar, scheduler', i find that i have very little knowledge about dojo, and AMD, so i decide to learn the dojo tutorial. So this is the 1st course, i want to write some notes about the key point.

and i will maintain a list contains the topics i need to read, just like a queue. I will add the topic when i find interesting ones, and remove the topic when i finish it. and i will assign every item in the list with a number.

## 2. why dojo choose AMD
1. allowing modular web application development.
2. AMD works with pure JavaScript.
3. allowing source files to work in browser.
4. also supporting a build process for producing optimized resources to enhance applicaiton in deployment.

## 3. what is avaiable after dojo.js in loaded

1. so dojo's AMD loader is avaiable
2. the loader define two global functions *require*  and *define*.
3. the require function enables you to load modules and use them
4. the define function allows you to define your own module.

## 4. We see two baisc modules for dom manipulation

1. dojo/dom
2. dojo/dom-construct
3. the first example use require to load modules:

```javascript
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Tutorial: Hello Dojo!</title>
</head>
<body>
    <h1 id="greeting">Hello</h1>
    <!-- load Dojo -->
    <script src="//ajax.googleapis.com/ajax/libs/dojo/1.10.4/dojo/dojo.js"
            data-dojo-config="async: true"></script>

    <script>
        require([
            'dojo/dom',
            'dojo/dom-construct'
        ], function (dom, domConstruct) {
            var greetingNode = dom.byId('greeting');
            domConstruct.place('<em> Dojo!</em>', greetingNode);
        });
    </script>
</body>
</html>
```

4. this examples is cool. And when use different dojo.js source, it gives totally different behavior in browser's network traffic.

with the dojo.js in CDN, it seems that the file is compressed and already include some basic modules in that file, so makes less network traffic.

with the source files download, you can see a lot of traffic you can see the dependencies hierarchy

## 5. require the directly used module

The AMD loader will automatically load all sub-dependencies for a requested module, so only the modules that you need to use directly should be in your dependency list.

this is interesting.

## 6. Define your own module

```javascript
define([
    // The dojo/dom module is required by this module, so it goes
    // in this list of dependencies.
    'dojo/dom'
], function(dom){
    // Once all modules in the dependency list have loaded, this
    // function is called to define the demo/myModule module.
    //
    // The dojo/dom module is passed as the first argument to this
    // function; additional modules in the dependency list would be
    // passed in as subsequent arguments.

    var oldText = {};

    // This returned object becomes the defined value of this module
    return {
        setText: function (id, text) {
            var node = dom.byId(id);
            oldText[id] = node.innerHTML;
            node.innerHTML = text;
        },

        restoreText: function (id) {
            var node = dom.byId(id);
            node.innerHTML = oldText[id];
            delete oldText[id];
        }
    };
});
```

1. the idea is use define function
2. list the dependencies needed as an array
3. define anonymous function as callback to the define function, so when all the depend modules are loaded ,the anonymous function is called
4. the anoymous function return a object as the module's interface to the caller/requirer

## 7. load modules in CDNs and locally

give a global dojoConfig object. i think the packages property is the key.

## 8. AMD plugin

This is accomplished via a special AMD module called a "plugin". Plugins can be required like any other module, but their special functionality is only activated by adding an exclamation point (bang) to the end of the module identifier. 

## 9. use the module loading side-effect and do not need a reference to module

In some cases, as with dojo/domReady, we only load a module for its side-effects and do not need a reference to it. The AMD loader has no way of knowing this — it always passes a reference to each module in the dependencies array to the callback function, so any modules for which you do not need to use the return value should be placed at the end of the dependency array, and references to them omitted from the parameter list to the callback function.


so here is the total information in *hello dojo*, quite a lot knowledge and let add the topic lists to read to the end chapter.





## The Last: Lists of Topic to Read:

1. [Asynchronous Module Definition (AMD)](https://github.com/amdjs/amdjs-api/wiki/AMD)
2. [Dojo's AMD tutorial](https://dojotoolkit.org/documentation/tutorials/1.10/modules)
3. [Modern Dojo](https://dojotoolkit.org/documentation/tutorials/1.10/modern_dojo/)
4. [Query](https://dojotoolkit.org/documentation/tutorials/1.10/using_query/) and [Event](https://dojotoolkit.org/documentation/tutorials/1.10/events/)
5. [Ajax](https://dojotoolkit.org/documentation/tutorials/1.10/ajax/)
6. [Template-based Widgets](https://dojotoolkit.org/documentation/tutorials/1.10/templated/)
7. [Dijit Widget](https://dojotoolkit.org/documentation/?ver=1.10#widgets)
8. [Core Concept](https://dojotoolkit.org/documentation/?ver=1.10#coreConcepts)
