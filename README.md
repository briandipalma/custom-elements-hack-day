---
layout: index
permalink: /index.html
title: custom-elements
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