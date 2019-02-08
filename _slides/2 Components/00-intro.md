# Components

---
### Reusing Components
Components can be reused as many times as you want:
```js
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

---
### Data Must Be a Function
A component’s data option must be a function
    - each instance can maintain an independent copy of the returned data object
```js
data: function () {
  return {
    count: 0
  }
}
```
---
### Organizing Components
It’s common for an app to be organized into a tree of nested components
- you might have components for a header, sidebar, and content area, each typically containing other components for navigation links, blog posts, etc.

- To use these components in templates, they must be registered so that Vue knows about them
 - global
 - local

---
### Component example
```js
// Define a new component called button-counter
Vue.component('button-counter', {
  data: function () { return { count: 0 } },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```
- Components are reusable Vue instances with a name, here '<button-counter>'

Since components are reusable Vue instances, they
- accept the same options as new Vue
    - data, computed, watch, methods, and lifecycle hooks
- the only exceptions are a few root-specific options like el


---
### Component Names
The name you give a component may depend on where you intend to use it
When using a component directly in the DOM (as opposed to in a string template or single-file component),follow W3C rules for custom tag names (all-lowercase, must contain a hyphen). 
    - this helps avoid conflicts with current and future HTML elements

---
### Name Casing
Two options for naming your components

With kebab-case
```js
Vue.component('my-component-name', { /* ... */ })
```
- also use kebab-case when referencing its custom element
    - '<my-component-name>'

With PascalCase
```js
Vue.component('MyComponentName', { /* ... */ })
```
- you can use either case when referencing its custom element
    - '<my-component-name>'
    - '<MyComponentName>'

---
### Global Registration
components created using Vue.component:
```js
Vue.component('component-a', { /* ... */ })
Vue.component('component-b', { /* ... */ })
Vue.component('component-c', { /* ... */ })
```
These components are globally registered 
- they can be used in the template of any root Vue instance created after registration
```js
new Vue({ el: '#app' })
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```
- all three of these components will also be available inside each other

---
### Local Registration
With building system like Webpack, globally registering components means that even when not using a component, it could still be included in the final build
    - unnecessarily increases the amount of JavaScript

Local registration means to define your components as plain JavaScript objects
```js
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }})
```
- locally registered components are not also available in subcomponents
- if you wanted ComponentA to be available in ComponentB:
```js
var ComponentA = { /* ... */ }
var ComponentB = {
  components: { 'component-a': ComponentA },
  // ...
}
```

---
### ES6 modules
With es6 you can write:
```js
import ComponentA from './ComponentA.vue'
export default {
  components: {
    ComponentA
  },
  // ...
}
```

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Registration types


What would happen if we provided an object?

---
### Passing Data
- Props are custom attributes you can register on a component
```js
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```
- a component can have as many props as you’d like 
- by default, any value can be passed to any prop

---
### Single Root Element
Components can only have one root element
```js
<h3>{{ title }}</h3>
```

At the very least, you’ll want to include the post’s content:

Include a parent when needed:
```js
<div class="blog-post">
  <h3>{{ title }}</h3>
  <div v-html="content"></div>
</div>
```

---
### Listening to Child Components Events
You can $emit and @ or v-on to raise and listen for events

```js
// <!-- inside the component -->
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>

// <!-- using the component -->
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
```

---
### Using v-model on Components
Custom events can also be used to create custom inputs that work with v-model. 

Remeber that
```js
<input v-model="searchText">
```
is equivalent to
```js
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

---
### Using v-model on Components
On a component, v-model does this:
```js
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event" // <--
></custom-input>
```
For this to actually work though, the input inside the component must
- bind the value attribute to a value prop
- on input, emit its own custom input event with the new value

```js
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input v-bind:value="value" v-on:input="$emit('input', $event.target.value)">
  `
})
```
Now v-model should work perfectly with this component

---
### Content Distribution
Just like with HTML elements, it’s often useful to be able to pass content to a component, like this:
```js
<alert-box>
  Something bad happened. // <!-- child content
</alert-box>
```
To transclude, use a slot element
```js
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box"><strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

There are named slots and scoped slots too, more later

---
### Dynamic Components
Sometimes, it’s useful to dynamically switch between components, like in a tabbed interface

```js
<!-- Component changes when currentTabComponent changes -->
<component v-bind:is="currentTabComponent"></component>
```

In the example above, currentTabComponent can contain either
- the name of a registered component
- a component’s options object

---
### DOM Template Parsing Caveats
Some HTML elements, such as 'ul', 'ol', 'table' and 'select' have restrictions on what elements can appear inside them, and some elements such as 'li', 'tr', and 'option' can only appear inside certain other elements.

This will lead to issues when using components with elements
```js
<table>
  <blog-post-row></blog-post-row>
</table>
```

The custom component <blog-post-row> will be hoisted out as invalid content,

---
### DOM Template Parsing Caveats (2)
The workaround
```js
<table>
  <tr is="blog-post-row"></tr>
</table>
```


---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Components

