Experimenting with Custom Elements.
===================================

As part of Caplin's Hoverboards and Flying Cars (What cool things should we be doing in 2014 and 2015?) hackday our team decided to investigate Custom Elements - a Web Components spec.

Web Components?
---------------

The [Web Components](http://w3c.github.io/webcomponents/explainer/) model consists of four specs.

* [Templates](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/templates/index.html)
* [Custom Elements](http://w3c.github.io/webcomponents/spec/custom/)
* [Shadow DOM](http://w3c.github.io/webcomponents/spec/shadow/)
* [HTML Imports](http://w3c.github.io/webcomponents/spec/imports/)
 
When used together the aim of these specs is to allow developers to easily create widgets that have the same power, ease of composition and encapsulation as native browser widgets. This should allow faster development and easy reuse of previously built or thirdparty widgets.




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
