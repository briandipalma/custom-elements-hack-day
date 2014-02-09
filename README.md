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

For the hack day, being time constrained, we chose to focus on only one of the specs, Custom Elements. Custom Elements are developer created custom DOM elements. 

There were two reasons driving the creation of the Custom Elements spec

* to allow Web developers to build their own, fully-featured DOM elements
* as part of the [Extensible Web Manifesto](http://extensiblewebmanifesto.org/) to take away the magic from browser elements by explaining them with primitives that developers have access to

For web developers there are several advantages of Custom Elements over the "DOM-as-a-render-view scaffolding" approach that they are currently forced to take.
Custom Elements are semantically richer, making it much easier to understand which elements map onto which application widget, providing a cleaner and clearer DOM structure.
They are treated the same as native elements by the browser this means they can use and can be used by standard platform DOM methods.
This allows them to be more easily composed compared to heterogeneous framework or library widgets.
It also means developers would only need to know the DOM APIs to be able to use Custom Elements unlike the current situation were it seems every six months a new framework or library gains popularity with its own APIs and concepts. 
The lifecycle of Custom Elements is handled by the browser, so the developer no longer needs to worry about trying to keep track of when a widget is added or removed from the DOM, the browser will
deal with those issues for him and provide appropriate notifications. They can also help with cohesiveness as they are the obvious place to store all the view logic relevant to a widget.

How do I create them?
---------------------

```javascript
var SimpleModalPrototype = Object.create(HTMLElement.prototype);

SimpleModalPrototype.createdCallback = function() {
  this.textContent = "I'm a simple-modal!";
};

var SimpleModal = document.registerElement('simple-modal', {
  prototype: SimpleModalPrototype
});
```
