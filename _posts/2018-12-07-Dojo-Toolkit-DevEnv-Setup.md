---
title: Dojo-Toolkit-DevEnv-Setup
date: 2018-12-07
tag: dojo dijit environment setup
---

How to setup the Dojo Development Environment
====

## 1. Some complaints

I think that life is a circle or a loop, you will always encounter the same thing or at least the very similar thing again and again, and this time I have to get familiar with the dijit, in order to know how to use the tree view.

Last time, I think it is no need to mastering this technique, but soon enough, the life proves it wrong, so i have to pick up the dojo toolkit learning tutorial, so I have to set up the Dev Environment

## 2. How to Setup the Dev Environment

Refer to this passage:
[Hello Dojo](https://dojotoolkit.org/documentation/tutorials/1.10/hello_dojo/index.html)

Simply speaking, You need to download the source code, extract it to a directory and make the structure like below:

```
demo/
    myModule.js
dojo/
dijit/
dojox/
util/
hellodojo.html
```
so your module live in the demo dir, and the rest is the dojo core, dijit, dojox and util.

you can load the dojo with this tag

```html
<script src="dojo/dojo.js"></script>
```

and update the dojo config object like this:

```javascript
var dojoConfig = {
    async: true,
    baseUrl: '.',
    packages: [
        'dojo',
        'dijit',
        'dojox',
        'demo'
    ]
};
```
