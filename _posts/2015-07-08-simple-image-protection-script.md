---
layout: post
title: Change “add to cart” button text
published: true
tags: 
  - javascript
---


It can often be handy to disable the browser context menu (the menu that pops up when you right click) on your website, partiularly if you have images that you would prefer no-one copies without consent.

I ran into this situation recently and put together a very simple script to prevent images being selected or the context menu being opened.

It doesn't prevent opening the console using `F12` or `shift` + `i`, nor does it prevent text selection.


```javascript

/*
* Copy Protection
*/

// Disable image drag
document.ondragstart = function() {
  return false;
}

// Disable context menu
document.oncontextmenu = function nocontext(e) {
  return false;
}
```
