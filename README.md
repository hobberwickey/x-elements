# XElements

XElements An extremely lightweight (less that 220 lines uncompressed) library to smooth out the rough edges of native webcomponents

## Basic Setup

To create a component like `<my-component status='active'></my-component>` you would do the following

### HTML Templates

```html
<template id='my-component'>
  <!-- 
    binding text and attributes to the components data 
    is added with the {{ }} syntax.  
  -->
  <h1>Welcome {{ user.first_name }}</h1>
  <div class="user {{ status }}">
    <p>A simple component to display user information</p>
    <ul>
      <li>{{ user.first_name }} {{ user.last_name }}</li>
      <li>{{ user.email }}</li>
      <li>{{ user.phone }}</li>
    </ul>
    <!--
      Binding events is handled too just add an attribute 'on-event-name'
      with the value "{{ methodToCall arg1 arg2... }}". Args can be
      bare integers, raw strings (use '' or ""), or values accessible from 
      the 'this' context
    -->
    <div class='btn' on-click="{{ updateUser user.id }}">Edit</div>
  </div>
</template>
```

### Javascript

```javascript
var XElements = require("x-elements");

class MyComponent extends XElements { 
  constructor() {
    super();
  }

  /* 
    This is same as native webcomponents 
  */
  static get observedAttributes() {
    return ["status"];
  }
  
  /* 
    This works similarly to observedAttributes, but
    objects and arrays can be assigned directly without
    the need to JSON encode/decode or set up a bunch of 
    getters and setters.

    This will just work

    myComponent.user = {
      first_name: "John", 
      last_name: "Doe"
      ...
    }
  */
  static get observedProperties() {
    return ["user"];
  }

  /*
    
   */
  connectedCallback() { 
    /*
      shadowDOM is turned off by default, more on that later
      this.__useShadow__ = true 
    */
    
    /*
      Calling __connect__ to attach your template to the component
     */
    this.__connect__("my-component")
  }

  firstConnected() {
    /*
      This method is called once after your component is attached 
      to the DOM and fully loaded. Do any setup or attaching event
      listeners here
     */
  }

  onUser(newValue, oldValue) {
    /*
      the attributeChangedCallback is still available in XElements
      but each observed property or attribute also has a 'onProperty'
      method, so you don't need to handle everything in a huge switch 
      or if/else statement. 

      Additionally, these methods will be called once after the firstConnected
      callback
     */
    
    /*
      A simple method for firing a custom event. The first argument is the 
      event name and the second is the data which will be accessible in the 
      standard 'event.detail' data
     */ 
    this.fire("updated", this.user)
  }

  on(propertyName, newValue, oldValue) {
    /*
      A catch all, shorthand for attributeChangedCallback
     */
  }
}

customElements.define('my-component', MyComponent);
module.exports = Auth;
```css

### CSS
```
  Since shadowDOM is turned off by default with XElements there's no 
  special styling required. You can 'encapsulate' your styles the same 
  way you would anything else, with just simple CSS

  my-component {
    display: block;
  }


  my-component h1 {
    font-size: 22px;
    color: #888;
  }

  ...
```

## Why is ShadowDOM turned off by default

While the idea of using the ShadowDOM is attractive, it's caused 
far more problems than it's solved. It's made form creation all but impossible, 
made tweaking styles difficult, and DOM manipulation cumbersome. What's worse is that 
while most of the web components standards work great across browsers, the ShadowDOM 
remains a mess that requires heavy handed shims. However web-components without 
the ShadowDOM already work great across browsers, and offer an excellent way to 
build component based UIs with clean, maintainable separation of JS/HTML/CSS.

There's no need to wait for the browsers to get their acts together, and no need 
to learn complicated, ever changing frameworks. Web Components are already great,
they just need some small tweaks.   