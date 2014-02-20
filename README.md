---
layout: index
permalink: /index.html
title: Custom-Elements
---

Experimenting with Custom Elements.
===================================

As part of Caplin's Hoverboards and Flying Cars (What cool things should we be doing in 2014 and 2015?) hack day our team decided to investigate Custom Elements - a Web Components spec.

Web Components?
---------------

The [Web Components](http://w3c.github.io/webcomponents/explainer/) model consists of four specifications.

* [Templates](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/templates/index.html)
* [Custom Elements](http://w3c.github.io/webcomponents/spec/custom/)
* [Shadow DOM](http://w3c.github.io/webcomponents/spec/shadow/)
* [HTML Imports](http://w3c.github.io/webcomponents/spec/imports/)
 
The specifications aims are to allow developers to create elements. 
These elements will have the same power, ease of composition and encapsulation as native browser elements.
This will allow for faster development and easier reuse of GUI components.

For the hack day, being time constrained, we chose to focus on only one of the specifications, Custom Elements. Custom Elements are developer created custom DOM elements. They are the foundation of Web Components.

There were two reasons driving the creation of the Custom Elements specification

* to allow Web developers to build their own DOM elements
* to explain how browser elements work

The drive for the second reason was the [Extensible Web Manifesto](http://extensiblewebmanifesto.org/).
It aims to take the magic away from the web platform. Providing an API for element definition and registration is part of that clarification.

What's in it for me?
--------------------

Currently developers use the DOM as view scaffolding. Custom Elements have several advantages over that approach.

Custom Elements allow clear semantics. It's easy to understand the mapping between elements and GUI component code. They result in a cleaner and clearer DOM structure.

The browser treats them as native elements. This means the browser can use them in its DOM methods.
This makes them easy to compose compared to heterogeneous framework or library components.
Developers only need to know the DOM APIs to be able to use Custom Elements. Currently almost every six months a new framework or library gains popularity. To use it involves learning its APIs and concepts.

The browser handles the life cycle of Custom Elements. The developer no longer needs to keep track of addition or removal of a component from the DOM. The browser will
deal with those issues for him and provide appropriate notifications. They also help cohesiveness as they are the obvious place to store all the view logic relevant to a component.

How do I make them?
---------------------

Making a Custom Element is simple. Create a JavaScript object which can fulfill some, all or none of the Custom Elements API (the element definition).
Then, using your chosen element name, register the object with the browser (element registration). This allows the browser to create your element when it's asked to.

The element definition is a small API. Note that the element definition must extend a DOM class.
The element definition serves as the `prototype` of the custom element instantiation.

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

The `attachedCallback` and `detachedCallback` names are the [subject of some debate](https://www.w3.org/Bugs/Public/show_bug.cgi?id=24314). So they may change.

The registration is simple, a call to the `document.registerElement` method.

```javascript
var CustomElement = document.registerElement('custom-element', {
  prototype: CustomElementPrototype
});
```

The first argument to the `document.registerElement` method is the name of the Custom Element. Custom Element names must contain a hyphen. 
This is to prevent name clashes between future browser element names and Custom Elements.
The second argument is the element registration object. Use it to specify the `prototype` of the Custom Element and what native element your Custom Element extends if it extends one.

### Wait, I can extend native elements? ###

Yes you can and it's quite trivial. There are a few changes required to the definition, registration and usage of the Custom Element though.

First you must extend the DOM element you wish to sub-type instead of the generic `HTMLElement`.

```javascript
//Extending a DOM button with Object.create().
var CustomButtonElementPrototype = Object.create(HTMLButtonElement.prototype);
```

Then you must specify the element tag for your element's super-type when registering.

```javascript
var CustomButtonElement = document.registerElement('custom-button-element', {
  prototype: CustomButtonElementPrototype,
  extends: "button"
});
```

And if you use HTML markup to create your element then you need to declare what type it is.

```HTML
<button is="custom-button-element"></button>
```

Can I use them?
---------------

Custom Elements is not standardized yet, they are at the Last Call Draft stage. That's not an impediment to using them though.
As usual with the web, implementation is leading standardization.

They are in [Chrome beta](https://code.google.com/p/chromium/issues/detail?id=180965) and Firefox [is implementing them](https://bugzilla.mozilla.org/show_bug.cgi?id=856140).
[The Custom Elements polyfill](https://github.com/polymer/CustomElements) provides Custom Elements support in other browsers. It supports IE10+ and all modern browsers.

The polyfill is the simplest way to support Custom Elements in browsers. It's available [via npm and bower](https://www.npmjs.org/package/customelements).

The [X-Tag](http://www.x-tags.org/index) polyfills support IE9+, they provide Custom Element and HTMLImport support.
There is also the [Polymer project](http://www.polymer-project.org/). It includes polyfills for many web standards such as Pointer Events. Their browser support is IE10+.

IE8 support is a possibility but not with the exact same workflow as in other browsers. The Custom Elements polyfill in IE9+ relies on DOM Mutation Events. Changing the DOM (node inserts or removals etc) fires these mutation events.
The polyfill reacts to these events by upgrading any inserted Custom Elements. Mutation Events, though, aren't supported in IE8. Meaning Custom Elements are never upgraded. Manual Custom Element upgrades for IE8 are a possible workaround.
It's not ideal and it takes away some of the benefits of Custom Elements but it allows for future friendly development.

How we built our Custom Elements
--------------------------------

For the hackday we used [Polymer platform](https://github.com/Polymer/platform). It's a collection of all the Polymer polyfills. If all you want is Custom Elements support it may be easier to use the stand alone polyfill or the
[X-Tag core polyfill](https://github.com/x-tag/core) which is smaller than the Polymer one.

We then started to create as many Custom Elements as we could. The first one was a wrapper around our current FxTile, it turned out to be easy to wrap the current set of widgets with custom elements.

```HTML
<caplin-tile currencyPair="EURUSD" amount="6000"></caplin-tile>
```

Adding one of our FxTiles became as easy as adding some markup. The `CaplinTile` Custom Element encapsulates all the logic of creating a tile.

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
We also experimented with changing the model via DOM attributes.
Changing the `currencyPair` or `amount` attribute modifies the tile via the model.

Wrapping existing widgets is not recommend when using Custom Elements but the lack of time meant we had to chose that route.
A better approach would have been to compose the `caplin-tile` from other Custom Elements.

```HTML
<caplin-tile>
	<caplin-tile-header></caplin-tile-header>
	<caplin-tile-one-way-panel>
		<button is="caplin-toggle-button" name="toggle-buy-sell"></button>
		<input is="caplin-fx-amount-input"></input>
		<button is="caplin-toggle-button" name="toggle-dealt-currency"></button>
		<input is="caplin-fx-date-picker"></input>
		<button is="caplin-fx-execute-button" side="bid"></button>
		<button is="caplin-fx-execute-button" side="ask"></button>
	</caplin-tile-one-way-panel>
</caplin-tile>
```

It was positive that it was so easy to integrate Custom Elements with existing UI components. It bodes well for upgrading to them in a phased approach.

We also wrapped our grid (`caplin-grid`) and fxticket (`caplin-outright-ticket`). To layout all these components we created a custom element tabbed panel.

```HTML
<caplin-tabbed-panel>
	<div name="Tile Set">
		<caplin-tile id='firsttile' currencyPair="GBPUSD" amount="5000"></caplin-tile>
		<caplin-tile currencyPair="EURUSD" amount="6000"></caplin-tile>
	</div>
</caplin-tabbed-panel>
<caplin-tabbed-panel>
	<caplin-grid name="Fx.Majors" grid="caplinx.fxblotters.majors"></caplin-grid>
	<div name="Bob">I am a native element</div>
	<caplin-grid name="Blotter" grid="caplinx.fxblotters.activity.blotter"></caplin-grid>
</caplin-tabbed-panel>
```

The `caplin-tabbed-panel` took its children and created a tab for each. Clicking a tab hides the currently visible child and displays the selected child.
The `name` attribute of each child was the text value of each tab. Custom Elements turned out to be a natural and pleasant way to build a GUI.

The `caplin-flex-box` followed which allowed for the resizing of its child elements.

```HTML
<caplin-flex-box direction="column">
	<caplin-tabbed-panel>
		...
	</caplin-tabbed-panel>
	<caplin-tabbed-panel>
		...
	</caplin-tabbed-panel>
</caplin-flex-box>
```

It uses the `direction` attribute to decide how to layout its child nodes. Either on top of each other (column) or side by side (row).
Inserting draggable splitters between the child nodes allowed resizing.

We had time to create a floating panel element that you could drag around the web page and which could contain other elements.
We then added one of the elements we created.

```javascript
var dialog = document.createElement('caplin-floating-panel');
var caplinOutrightTicket = document.createElement("caplin-outright-ticket");

dialog.appendChild(caplinOutrightTicket);

document.body.appendChild(dialog);
```

The code above is understandable by almost any web developer.
Platform knowledge is all that's needed to work with Custom Elements.

Verdict
-------

Thinking of layout panels, components and controls as elements is a simple but powerful concept.
These is no mystery in how they work or how to use them, it's DOM 101.
They allow web developers to create cohesive, transparent and composable UI components.

So, Custom Elements. Two thumbs up.
