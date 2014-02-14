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

For the hack day, being time constrained, we chose to focus on only one of the specifications, Custom Elements. Custom Elements are developer created custom DOM elements. 

There were two reasons driving the creation of the Custom Elements specification

* to allow Web developers to build their own DOM elements
* to explain how browser elements work

The drive for the second reason was the [Extensible Web Manifesto](http://extensiblewebmanifesto.org/).
It aims to take the magic away from the web platform. Providing an API for element definition and registration is part of that clarification.

What's in it for me?
--------------------

Currently developers use the DOM as view scaffolding. Custom Elements have several advantages over that approach though. 
Custom Elements allow clear semantics. It's easy to understand the mapping between elements and GUI component code. They result in a cleaner and clearer DOM structure.

The browser treats them as native elements. This means the browser can use them in its DOM methods.
This makes them easy to compose compared to heterogeneous framework or library components.
Developers only need to know the DOM APIs to be able to use Custom Elements. Currently almost every six months a new framework or library gains popularity. To use it involves learning its APIs and concepts.

The browser handles the life cycle of Custom Elements. The developer no longer needs to keep track of addition or removal of a component from the DOM. The browser will
deal with those issues for him and provide appropriate notifications. They also help cohesiveness as they are the obvious place to store all the view logic relevant to a component.

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

Custom Elements is a small specification and it hasn't been standardized yet, but they can still be used. As usual with the web, implementation is leading standardization.
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
Another aspect of Custom Elements we experimented with was changing the model based on DOM attributes changing.
So if you changed the `currencyPair` or `amount` attribute you would modify the model and therefore the tile.

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

It was positive that it was so easy to integrate Custom Elements with already existing widgets. It bodes well for upgrading to them in a phased approach.

Along with wrapping a Caplin tile we wrapped our grid (`caplin-grid`), fxticket (`caplin-outright-ticket`) and created a custom element tabbed panel.

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

The `caplin-tabbed-panel` took its children and created a tab for each, clicking on a tab would display the child and hide the previously displayed one.
The `name` attribute of each child was the text value of each tab. Custom Elements turned out to be a natural and pleasant way to build a GUI.

To allow us to resize the `caplin-tabbed-panel` a `caplin-flex-box` was created.

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
Draggable splitters are inserted between the child nodes to allow resizing.

We had time to create a floating panel element that you could drag around the web page and which could contain other elements.
So of course we added one of the elements we created.

```javascript
var dialog = document.createElement('caplin-floating-panel');
var caplinOutrightTicket = document.createElement("caplin-outright-ticket");

dialog.appendChild(caplinOutrightTicket);

document.body.appendChild(dialog);
```

The code above can be understood by almost any web developer.
Platform knowledge is all that's needed to work with Custom Elements.

Verdict
-------

Conceptually it makes sense to think of layout panels, widgets and controls as elements.
These is no mystery as to how they work or how to use them, it's DOM 101.
They allow web developers to create cohesive, transparent and composable UI components.

So, Custom Elements. Two thumbs up.
