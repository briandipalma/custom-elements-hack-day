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

The first argument to the `document.registerElement` method is the name of the Custom Element. A rule that Custom Element names must follow is that they must contain a hyphen; this allows new native elements to be added to browsers without causing name clashes with Custom Elements.
The second argument are the element registration options that specify the prototype of the Custom Element and what native element your Custom Element extends if it extends one.

### Wait, I can extend native elements? ###

Yes you can and it's quite trivial. There are a few changes required to the definition, registration and usage of the Custom Element though.

Firstly you must extend the DOM element you wish to sub-type instead of the generic `HTMLElement`.

```javascript
//Extending a DOM button with Object.create().
var CustomButtonElementPrototype = Object.create(HTMLButtonElement.prototype);
```

Then you must specify the element tag for the your elements super-type when registering.

```javascript
var CustomButtonElement = document.registerElement('custom-button-element', {
  prototype: CustomButtonElementPrototype,
  extends: "button"
});
```

And finally if you plan on using HTML markup to create your element then you need to declare what type it is.

```HTML
<button is="custom-button-element"></button>
```

Can I use them?
---------------

Custom Elements is a small spec and it hasn't been standardized yet, but they can still be used. As usual with the web, implementation is leading standardization.
They are about to [roll into stable Chrome](https://code.google.com/p/chromium/issues/detail?id=180965) with Chrome 33 and Firefox [is close behind](https://bugzilla.mozilla.org/show_bug.cgi?id=856140).
For other browsers there are ways of supporting Custom Elements via [the Custom Elements polyfill](https://github.com/polymer/CustomElements), this polyfill is tested to work with IE10+ and all modern browsers.

The polyfill is the simplest way to support Custom Elements in all browsers (it's available [via npm and bower](https://www.npmjs.org/package/customelements)) but there are other ways.

The [X-Tag](http://www.x-tags.org/index) project includes polyfills that allow it to support IE9+, it provides Custom Element and HTMLImport support.
There is also the more broad scoped [Polymer project](http://www.polymer-project.org/) they include polyfills for many new web standards including Pointer Events, their browser support is IE10+.

IE8 support may be a possibility but not with the exact same workflow as in other browsers. Custom Elements in IE9+ are supported by using DOM Mutation Events, these are events which fire when the DOM is changed (node inserts or removals etc).
The polyfill reacts to and upgrades any newly inserted Custom Elements but Mutation Events aren't supported in IE8 so Custom Elements are never upgraded. The workaround that suggests itself is for the developer to manually trigger a Custom Element upgrade in IE8.
It's not ideal and it takes away some of the benefits of Custom Elements but it allows future friendly development.

How we built our Custom Elements
--------------------------------

For the hackday we decided to use [Polymer platform](https://github.com/Polymer/platform) which is a collection of all Polymer polyfills. If all you want is Custom Elements support it may be easier to use the stand alone polyfill or the
[X-Tag core polyfill](https://github.com/x-tag/core) which is smaller than the Polymer one.

We then started to create as many Custom Elements as we could. The first one was a wrapper around our current FxTile, it turned out to be easy to wrap the current set of widgets with custom elements.

```HTML
<caplin-tile currencyPair="EURUSD" amount="6000"></caplin-tile>
```

Adding one of our FxTiles became as easy as adding some markup. All the logic of creating a tile was encapsulated within our `CaplinTile` class.

```javascript
var CaplinTilePrototype = Object.create(HTMLElement.prototype);

CaplinTilePrototype.attachedCallback = function() {
	var amount = this.getAttribute("amount"),
		currencyPair = this.getAttribute("currencyPair");
    
    //Create an FxTile and get the tile element - tileElement.

    this.appendChild(tileElement);
};

CaplinTilePrototype.attributeChangedCallback = function(name, oldValue, newValue) {
	//Handle DOM attribute changes by notifying the tile.
};

document.registerElement("caplin-tile", {prototype: CaplinTilePrototype});
```

The `this` pointer for a Custom Element is the DOM element, the `appendChild` is happening on the Custom Element.
There's no need for any framework or library specific API, just use well known DOM APIs to build your view.
Another aspect of Custom Elements we experimented with was changing the model based on DOM attribute values changing on the element.