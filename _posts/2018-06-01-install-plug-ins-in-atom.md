---
title: "Install plug-ins to ATOM editor"
date: 2018-06-01
tag: front-end development, atom
excerpt_separator: <!--more-->
---

install some plugins in atom editor to help with front-end development
<!--more-->
## First installed plug-ins

### 1. The Plugin List

  1. emmet: for editing HTML
  2. atom-beautify: for better display various kinds of source code
  3. autocomplete-paths: Very often, your code will need to refer to other files
     and folders in your project. This plug-in helps by offering filenames in an
     autocomplete menu as you type.
  4. api-docs:  lets you look up documentation based on keyword. It displays the
     documentation in a separate tab in the editor.
  5. linter: A linter is a program that checks the syntax and style of your code
    5.1 linter-csslint:  ensures that your CSS is syntactically correct and also
        offers suggestions about writing performant CSS.
    5.2 linter-eslint:
    5.3 linter-htmlhint: i do not know why this plug-in is called hint but not lint

### 2. Something about the docs

  1. The Mozilla Developer Network: you have two ways to access it:
    1.1 [https://devdocs.io](https://devdocs.io) and i believe that is so cool about this site
    1.2 [https://developer.mozilla.org](https://developer.mozilla.org)

### 3. Something called crash course on CLI, which i think i don't need

### 4. Forth: Install Node.js, which i already done by yesterday

  1. Node.js i think is a runtime for JavaScript
  2. browser-sync is a tool based on Node.js,  It makes your example code easier
     to run in the browser and automatically reloads the browser when you save
     changes to your code. which i think is pretty cool for the developer

     And i already installed browser-sync, but don't know how to use it. so bacisly
     browser-sync is a command line tool that serves the developing process.

     after the first 2 projects, and I will understand how to use this tool

  3. So i use atom finished editing a index.html file, and now to see the result.
  4. Sees pretty cool, and i added an favicon.ico file, makes html page seems
     even better.

### 5. browser-sync Usage

  1. Just type:
    browser-sync start --server --files "stylesheet/\*.css \*.html"

  but the above command does not do the live reload work for me, so i checked the
  browser-sync help usage, there is a command called --watch, when i add this on,
  it totally worked, so here is the whole command:

  $browser-sync start --server --watch --file "stylesheets\*.css *.html"

  and it feels cool
