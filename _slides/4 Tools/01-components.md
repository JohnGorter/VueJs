# Components

---
### Composing applications

<img src="/images/components.png" width=85%>

---
### Composing applications (2)
Example structure
```js
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
``` 

---
### Component definition
- essentially a Vue instance with pre-defined options
- define it
```js
// Define a new component called todo-item
Vue.component('todo-item', {
  template: '<li>This is a todo</li>'
})
```
- use it
```js
<ol>
  <todo-item></todo-item>
</ol>
```

---
### Pass data 
- pass data from parent to child by defining props
```js
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```
- pass data using v-bind
```js
<div id="app-7">
  <ol>
    <todo-item v-for="item in groceryList" v-bind:todo="item"></todo-item>
  </ol>
</div>
```

---
### Vue Components vs Custom Elements
Similarities 
- implement the Slot API 
- the 'is' special attribute

Differences:
- Spec is finalized, but not implemented in every browser
- Vue components 
  - don’t require any polyfills 
  - work consistently in all supported browsers 
  - support cross-component data flow
  - support custom event communication
  - have build tool integrations

* When needed, Vue components can be wrapped inside native custom element




<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Using plain JavaScript

---

<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Create a component using plain JavaScript
