---
{
  "name": "Binding: Basics",
  "culture": "en-US",
  "description": "The basics of data-binding with Aurelia.",
  "engines" : { "aurelia-doc" : "^1.0.0" },
  "author": {
    "name": "Jeremy Danyow",
  	"url": "http://danyow.net"
  },
  "contributors": [],
  "translators": [],
  "keywords": ["JavaScript", "Data Binding"]
}
---

## [Introduction](aurelia-doc://section/1/version/1.0.0)

This article covers the basics of data-binding with Aurelia. You'll learn how to bind to HTML attributes, DOM Events and element content. You'll also see how to give your view-models references to DOM elements, making it easy work with elements directly.

## [HTML and SVG Attributes](aurelia-doc://section/2/version/1.0.0)

Aurelia supports binding HTML and SVG attributes to JavaScript expressions. Attribute binding declarations have three parts: `attribute.command="expression"`.

* `attribute`:  an HTML or SVG attribute name.
* `command`: one of Aurelia's attribute binding commands:
  * `one-time`: flows data one direction: from the view-model to the view, **once**.
  * `one-way`: flows data one direction: from the view-model to the view.
  * `two-way`: flows data both ways: from view-model to view and from view to view-model.
  * `bind`: automically chooses the binding mode. Uses two-way binding for form controls and one-way binding for almost everything else.
* `expression`: a JavaScript expression.

Typically you'll use the `bind` command since it does what you intend most of the time.  Consider using `one-time` in performance critical situations where the data never changes because it skips the overhead of observing the view-model for changes. Below are a few examples.

<code-listing heading="HTML Attribute Binding Examples">
  <source-code lang="HTML">
    <input type="text" value.bind="firstName">
    <input type="text" value.two-way="lastName">

    <a class="external-link" href.bind="profile.blogUrl">Blog</a>
    <a class="external-link" href.one-way="profile.twitterUrl">Twitter</a>
    <a class="external-link" href.one-time="profile.linkedInUrl">LinkedIn</a>
  </source-code>
</code-listing>

The first input uses the `bind` command which will automatically create `two-way` bindings for input value attribute bindings.  The second input uses the `two-way` command which explicitly sets the binding mode. The first anchor element uses the `bind` command which will automatically create a `one-way` binding for anchor href attributes. The other two anchor elements use the `one-way` and `one-time` commands to explicitly set the binding's mode.

## [DOM Events](aurelia-doc://section/3/version/1.0.0)

The binding system supports binding to DOM events.  A DOM event binding will execute a JavaScript expression whenever the specified DOM event occurs. Event binding declarations have three parts: `event.command="expression"`.

* `event`:  the name of a DOM event, without the "on" prefix.
* `command`: one of Aurelia's event binding commands:
  * `trigger`: attaches an event handler directly to the element. When the event fires, the expression will be invoked.  
  * `delegate`: attaches a single event handler to the document (or nearest shadow DOM boundary) which handles all events of the specified type, properly dispatching them back to their original targets for invocation of the associated expression.
* `expression`: a JavaScript expression. Use the special `$event` property to access the DOM event in your binding expression.

Below are a few examples.

<code-listing heading="DOM Event Binding Examples">
  <source-code lang="HTML">    
    <button type="button" click.trigger="cancel()">Cancel</button>

    <button type="button" click.delegate="select('yes')">Yes</button>
    <button type="button" click.delegate="select('no')">No</button>

    <input type="text" blur.trigger="elementBlurred($event.target)">    
    <input type="text" change.delegate="lastName = $event.target.value">
  </source-code>
</code-listing>

The cancel button uses the `trigger` command to attach an event listener to the button element that will call the view-model's cancel method.  The yes and no buttons use the `delegate` command which will use the event delegation pattern.  The input elements have binding expressions that use the special `$event` property to access the [DOM event](https://developer.mozilla.org/en-US/docs/Web/API/Event).

## [Function References](aurelia-doc://section/4/version/1.0.0)

While developing custom elements or custom attributes you may encounter a situation where you have a `@bindable` property that expects a reference to a function. Use the `call` binding command to declare and pass a function to the bindable property. The `call` command is superior to the `bind` command for this use-case because it will execute the function in the correct context, ensuring `this` is what you expect it to be.

<code-listing heading="Simple call binding">
  <source-code lang="HTML">
    <my-element go.call="doSomething()"></my-element>

    <input type="text" value.bind="taskName"
    <my-element go.call="doSomething(taskName)"></my-element>
  </source-code>
</code-listing>

Your custom element or attribute can invoke the function that was passed to the `@bindable` property using standard call syntax: `this.go();`. If you need to invoke the function with arguments, create an object whose keys are the argument names and whose values are the argument values, then invoke the function with this "arguments object". The object's properties will be available for data-binding in the `call` binding expression.  For example, invoking the function with `this.go({ x: 5, y: -22, z: 11})`) will make `x`, `y` and `z` available for binding:

<code-listing heading="Accessing the call argument properties">
  <source-code lang="HTML">
    <my-element execute.call="doSomething(x, y)"></my-element>
  </source-code>
</code-listing>

## [Referencing Elements](aurelia-doc://section/5/version/1.0.0)

Use the `ref` binding command to create a reference to a DOM element. The ref command's most basic syntax is `ref="expression"`. When the view is data-bound the specified expression will be assigned the DOM element.

<code-listing heading="Simple ref example">
  <source-code lang="HTML">
    <template>
      <input type="text" ref="nameInput"> ${nameInput.value}
    </template>
  </source-code>
</code-listing>

The `ref` command has several qualifiers you can use in conjunction with custom elements and attributes:

* `element.ref="expression"`: create a reference to the DOM element (same as `ref="expression"`).
* `attribute-name.ref="expression"`: create a reference to a custom attribute's view-model.
* `view-model.ref="expression"`: create a reference to a custom element's view-model.
* `view.ref="expression"`: create a reference to a custom element's view instance (not an HTML Element).
* `controller.ref="expression"`: create a reference to a custom element's controller instance.

## [String Interpolation](aurelia-doc://section/6/version/1.0.0)

String interpolation expressions enable interpolating (surprise!) the result of an expression with text.  The best way to demonstrate this capability is with an example. Below are two span elements with data-bound textcontent:

<code-listing heading="Simple ref example">
  <source-code lang="HTML">
    <span textcontent.bind="'Hello' + firstName"></span>

    <span>Hello ${firstName}</span>
  </source-code>
</code-listing>

The first span uses the `bind` command. The second uses string interpolation.  The interpolated version is much easier to read and easy to remember because the syntax matches the [template literal](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) syntax standardized in ES2015/ES6.

String interpolation can be used within html attributes as an alternative to one-way binding. By default, the mode of an interpolation binding is `one-way` and the result of the expression is always coerced to a string. Results that are `null` or `undefined` will result in empty string.
