Experimenting with Custom Elements.
===================================

As part of our Hoverboards and Flying Cars (What cool things should we be doing in 2014 and 2015?) hackday our team decided to investigate Custom Elements - a Web Components spec.

Web Components
--------------

The [Web Components](http://w3c.github.io/webcomponents/explainer/) model consists of four specs.

* [Custom Elements](http://w3c.github.io/webcomponents/spec/custom/)
* [Shadow DOM](http://w3c.github.io/webcomponents/spec/shadow/)
* [Templates](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/templates/index.html)
* [HTML Imports](http://w3c.github.io/webcomponents/spec/imports/)

```javascript
var SimpleModalPrototype = Object.create(HTMLElement.prototype);

SimpleModalPrototype.createdCallback = function() {
  this.textContent = "I'm a simple-modal!";
};

var SimpleModal = document.registerElement('simple-modal', {
  prototype: SimpleModalPrototype
});
```

Semantically richer, can use normal DOM methods and standard platform methods, lifecycle is handled by browser, more cohesiveness "the responsibility of understanding how operations affect elements lies with the UA, which has comprehensive knowledge of this now and into the future, and not with the author."


http://extensiblewebmanifesto.org/
