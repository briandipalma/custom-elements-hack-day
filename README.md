Experimenting with Custom Elements.
===================================

As part of our Hoverboards and Flying Cars (What cool things should we be doing in 2014 and 2015?) hackday our team decided to investigate Custom Elements - a Web Components spec.

Short explaination of Web Components and specs that are part of it.

* Custom Elements
* Shadow DOM
* Templates

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
