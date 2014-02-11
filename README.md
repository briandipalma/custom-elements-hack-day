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

What's in it for me?
--------------------

For web developers there are several advantages of Custom Elements over the "DOM-as-a-render-view scaffolding" approach that they are currently forced to take.
Custom Elements are semantically richer, making it much easier to understand which elements map onto which application widget, providing a cleaner and clearer DOM structure.

They are treated the same as native elements by the browser this means they can use and can be used by standard platform DOM methods.
This allows them to be more easily composed compared to heterogeneous framework or library widgets.
It also means developers would only need to know the DOM APIs to be able to use Custom Elements unlike the current situation were it seems every six months a new framework or library gains popularity with its own APIs and concepts.

The lifecycle of Custom Elements is handled by the browser, so the developer no longer needs to worry about trying to keep track of when a widget is added or removed from the DOM, the browser will
deal with those issues for him and provide appropriate notifications. They can also help with cohesiveness as they are the obvious place to store all the view logic relevant to a widget.

How do I create them?
---------------------

There isn't much to creating a Custom Element, firstly you must create a JavaScript object which can fulfill some, all or none of the Custom Elements API (the element definition) and then you must register that JavaScript object, along with the Custom Element name, with the browser (element registration) so it can create your element when it's asked to.

The element definition is a small API, one point to note though is that the JavaScript object that is used for the Custom Element definition must extend a DOM class.
The element definition serves as the prototype of the custom element instantiation.

Here's an example of a Custom Element definition.

```javascript
//Extending a DOM class with Object.create().
var CustomElementPrototype = Object.create(HTMLElement.prototype);

CustomElementPrototype.createdCallback = function() {
	//called when a custom element is created.
};

CustomElementPrototype.attachedCallback = function() {
	//called when a custom element is inserted into the DOM.
};

CustomElementPrototype.detachedCallback = function() {
	//called when a custom element is removed from the DOM.
};

CustomElementPrototype.attributeChangedCallback = function(attributeName, oldValue, newValue) {
	//called when a custom element's attribute value has changed.
};
```

Note that the `attachedCallback` and `detachedCallback` method names are the [subject of some debate](https://www.w3.org/Bugs/Public/show_bug.cgi?id=24314) so they may change.

The registration is very simple, a call to the `document.registerElement` method.

```javascript
var CustomElement = document.registerElement('custom-element', {
  prototype: CustomElementPrototype
});
```

The first argument to the `document.registerElement` method is the name of the Custom Element. A rule that Custom Element names must follow is that they must contain a hyphen; this allows new native elements to be added to browsers without triggering name clashes with Custom Elements.

This is one of the advantages to using libraries like [X-Tag](http://www.x-tags.org/index) or the more broad scoped [Polymer](http://www.polymer-project.org/) they will

```javascript
var SimpleModalPrototype = Object.create(HTMLElement.prototype);

SimpleModalPrototype.createdCallback = function() {
  this.textContent = "I'm a simple-modal!";
};

var SimpleModal = document.registerElement('simple-modal', {
  prototype: SimpleModalPrototype
});
```
