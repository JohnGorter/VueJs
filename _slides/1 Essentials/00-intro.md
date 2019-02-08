# Essentials

---
### Essentials
- Declarative rendering
- Conditionals and Loops
- User Input
- Composing applications

---
### Declarative Rendering

template:
```js
<div id="app">
  {{ message }}
</div>
```

script:
```js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
* app instance gets message property


---
### Conditionals and Loops

```js
<div id="app-3">
  <span v-if="seen">Now you see me</span>
</div>

var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```
* binds to structure of DOM

---
### Conditionals and Loops (2)

```js
<div id="app-4">
  <ol><li v-for="todo in todos">
      {{ todo.text }}
  </li></ol>
</div>

var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
});
```
- app4.todos.push({ text: 'New item' })


---
### User input
Handling button clicks
```js
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>

var app5 = new Vue({
  el: '#app-5',
  data: { message: 'Hello Vue.js!' },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```

---
###  User Input (2)
Two-way binding between form input and app state
```js
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>

var app6 = new Vue({
  el: '#app-6',
  data: { message: 'Hello Vue!' }
})
```

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

---
### Vue Components vs Custom Elements
Differences:
- Spec is finalized, but not implemented in every browser
- Vue components 
  - don’t require any polyfills 
  - work consistently in all supported browsers 
  - support cross-component data flow
  - support custom event communication
  - have build tool integrations

* When needed, Vue components can be wrapped inside native custom element


---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Hello VueJS


