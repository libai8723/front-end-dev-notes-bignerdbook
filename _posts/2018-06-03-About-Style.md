---
title: The 3rd Chapter about style
date: 2018-06-03
tag: "front-end development" CSS
excerpt_separator: <!--more-->
---

一些最近本的关于style和CSS的概念
<!--more-->

## The Third Chapter about style

### 1. Two approaches to styling a web site

1. bottom up AKA: atomic style
2. top down

### 2. Styling Baseline

1. we can use a normalize.css file as a baseline to helps the CSS written by us display consistently across browsers.
2. when the book is written the version of normalize.css is 3.0.3, but by now it is 8.0.0, seem very cool to have this one.
3. remember to put the normalize.css file link before your own css file.

### 3. Prepare for styling

1. we add some class attribute to tags
2. we add logo-text to H1 tags
3. we add thumbnail-list to UL tags
4. we add thumbnail-item to LI tags
5. we add thumbnail-text to SPAN tags
6. we add thumbnail-image to IMG tags

### 4. what is a style rule

A style rule looks like below:

```css
    header, footer {
        margin: 0;
        padding: 8px 4px;
        background: cornflowerblue;
        color: inherit;
    }
```

1. header, footer is called selector
2. margin is property, 0 is its values;
3. margin: 0; is a declaration.
4. these 4 declararions makes a declaration block;
5. that's all

### 5. Box model

from the outside to the inside is : margin -> border -> padding -> content

### 6. Relationship Selector

a lot of rules, please refer to devdocs or MDN

### 7. Add a font is tricky, see the code below

```css
    @font-face {
        font-family: 'lakeshore';
        src: url('fonts/LAKESHOR-webfont.eot');
        src: url('fonts/LAKESHOR-webfont.eot?#iefix') format('embedded-opentype'),
            url('fonts/LAKESHOR-webfont.woff') format('woff'),
            url('fonts/LAKESHOR-webfont.ttf') format('truetype'),
            url('fonts/LAKESHOR-webfont.svg#lakeshore') format('svg');
        font-weight: normal;
        font-style: normal;
    }

    //can be refer as :
    selector {
    font-family: lakeshore;
    }
```

### 8. you have to know the css rules has Specificity

the more specific the rules will be more probable to effect the element.

### 9. Tips

**display: block;** means that the element will occupy the entire width allowed by their containing element.

**width: 100%;** set the width to 100%, which constrains it to the width of its container

**use two similar declaration** to support more browser

```css
.thumbnail-item {
    border: 1px solid rgb(100%, 100%, 100%);
    border: 1px solid rgba(100%, 100%, 100%, 0.8);
    }
```
