---
title: "Configuring Dojo with dojoConfig"
date: 2018-10-05
tag: Mendix, Dojo, JavaScript
---

## Configuring Dojo with dojoConfig

### 1. one of the getting started series

I think this chapter is important, so i read it first, and it seems important.

### 2. The Global dojoConfig object

1. The dojoConfig object (known as djConfig prior to Dojo 1.6, they are equivalent) is the primary mechanism for configuring Dojo in a web page or application.

2. The dojoConfig definition must comes before the dojo.js loaded, or else it will be ignored.

3. It is important to note the distinction between dojoConfig and dojo/_base/config. During the bootstrap process, dojo/_base/config is populated from these parameters for later lookup by module code.

4. so that is to say parameters in config contains dojoConfig's value.

### 3. dojoConfig Example

```javascript
<!-- set Dojo configuration, load Dojo -->
<script>
    dojoConfig= {
        has: {
            "dojo-firebug": true
        },
        parseOnLoad: false,
        foo: "bar",
        async: true
    };
</script>
<script src="//ajax.googleapis.com/ajax/libs/dojo/1.10.4/dojo/dojo.js"></script>

<script>
// Require the registry, parser, Dialog, and wait for domReady
require(["dijit/registry", "dojo/parser", "dojo/json", "dojo/_base/config", "dijit/Dialog", "dojo/domReady!"]
, function(registry, parser, JSON, config) {
    // Explicitly parse the page
    parser.parse();
    // Find the dialog
    var dialog = registry.byId("dialog");
    // Set the content equal to what dojo.config is
    dialog.set("content", "<pre>" + JSON.stringify(config, null, "\t") + "```");
    // Show the dialog
    dialog.show();
});
</script>

<!-- and later in the page -->
<div id="dialog" data-dojo-type="dijit/Dialog" data-dojo-props="title: 'dojoConfig / dojo/_base/config'"></div>
```

in this example, the author deliveries a lot message. i will list the ones i understand:

1. has is a feature detection, which i think need to read through
2. with **foo** property, means that you can add custom property.
3. parseOnLoad, meaning that with dojo you can build web app program programmaticlly and declaritively, so parse means parse the html page. it is the last line of body.
4. Author also import a digit widgts named dialog, it seem pretty cool.
5. Also author use a JSON module to do the stringify work, the convert config into formated string.

## 4. Possible Properties of dojoConfig

1. has() used for feature detection. Actually i do not know what is feature detection.so i add this to the list.
2. Debug/Firebug Lite.
3. Loader Configuration: see the topic 10 below.
    1. baseUrl:The base URL prepended to a module identifier when converting it to a path or URL
    2. packages: An array of objects which provide the package name and location
    3. map: which i do not understand. @todo
    4. cacheBust: If true, appends the time as a querystring to each module URL to avoid module caching
    5. By using the packages configuration, we've made all references to demo/* point to our local directory other than the CDN.

### 5. Locale and internationalization Exmaple

here is the code:

```javascript
<script>
    var dojoConfig = {
        has: {
            "dojo-firebug": true,
            "dojo-debug-messages": true
        },
        parseOnLoad: true,
        // look for a locale=xx query string param, else default to 'en-us'
        locale: location.search.match(/locale=([\w\-]+)/) ? RegExp.$1 : "en-us"
    };
</script>
<script src="//ajax.googleapis.com/ajax/libs/dojo/1.10.4/dojo/dojo.js"></script>
<script>
    require(["dojo/date/locale", "dijit/Dialog", "dojo/json", "dojo/_base/config",
    "dojo/_base/window", "dojo/i18n", "dojo/domReady!"]
    , function(locale, Dialog, JSON, config, win) {
        var now = new Date();
        var dialog = new Dialog({
            id: "dialog",
            // set a title on the dialog of today's date,
            // using a localized date format
            title: "Today: " + locale.format(now, {
                    formatLength:"full",
                    selector:"date"
            })
        }).placeAt(win.body());
        dialog.startup();

        dialog.set("content", "<pre>" + JSON.stringify(config, null, "\t") + "```");
        dialog.show();
    });
</script>
```

with this exmaple, you can see the locale module also read the configuration from the dojoConfig:

1. window.location.search is the URL parameter, so we can test the page with different URL parameter.

```javascript
locale: location.search.match(/locale=([\w\-]+)/) ? RegExp.$1 : "en-us";
```

2. This example is totally programmatically, the dialog is created in the call function, and placed with the dojo/_base/window module. Seems cool.

## The Last: Lists of Topic to Read

sadlly the todo list becomes longer... let us continue.

1. [1. Asynchronous Module Definition (AMD)](https://github.com/amdjs/amdjs-api/wiki/AMD)
2. [2. Dojo's AMD tutorial](https://dojotoolkit.org/documentation/tutorials/1.10/modules)
3. [4. Query](https://dojotoolkit.org/documentation/tutorials/1.10/using_query/) and [Event](https://dojotoolkit.org/documentation/tutorials/1.10/events/)
4. [5. Ajax](https://dojotoolkit.org/documentation/tutorials/1.10/ajax/)
5. [6. Template-based Widgets](https://dojotoolkit.org/documentation/tutorials/1.10/templated/)
6. [7. Dijit Widget](https://dojotoolkit.org/documentation/?ver=1.10#widgets)
7. [8. Core Concept](https://dojotoolkit.org/documentation/?ver=1.10#coreConcepts)
8. [9. Feature Detection](http://)
9. [10. Advanced AMD Usage tutorial](https://dojotoolkit.org/documentation/tutorials/1.10/modules_advanced/)
10. [The Dojo Loader: More Details](https://dojotoolkit.org/reference-guide/1.10/loader/amd.html)
