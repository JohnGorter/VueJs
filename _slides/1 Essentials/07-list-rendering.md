# List Rendering

---
### V-For
Directive to render a list of items based on an array
```js
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>

var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

---
### Parent access
Inside v-for blocks
- full access to parent scope properties
- support for optional second argument
```js
<ul id="example">
  <li v-for="(item, index) in items">{{ pMessage }} - {{ index }} - {{ item.message }}</li>
</ul>
var example2 = new Vue({
  el: '#example',
  data: {
    pMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

---
### v-for
Loop through properties of an object
```js
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }}: {{ value }}
</div>
```


---
### Keys
V-for uses an “in-place patch” strategy
  - similar to track-by="$index" in Vue 1.x
Efficient
  - suitable when list does not rely on child component state or temporary DOM state

Provide a unique key attribute for each item
```js
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

---
### Array Change Detection
Observed array’s mutation methods trigger view updates
- push()
- pop()
- shift()
- unshift()
- splice()
- sort()
- reverse()


---
### Caveats
Changes in array are not detected
```js
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // is NOT reactive
vm.items.length = 2 // is NOT reactive
```
use set
```js
Vue.set(vm.items, indexOfItem, newValue)
// - or - 
vm.items.splice(indexOfItem, 1, newValue)
// - or - 
vm.$set(vm.items, indexOfItem, newValue)
```

---
### Object Change Detection Caveats
Due to limitations of modern JavaScript, no detection of property additions or deletions
```js
var vm = new Vue({
  data: { a: 1 }
})
// `vm.a` is now reactive
vm.b = 2
// `vm.b` is NOT reactive
```
use set
```js
Vue.set(vm.userProfile, 'age', 27)
// - or - 
vm.$set(vm.userProfile, 'age', 27)
// - or multiple props - 
vm.userProfile = Object.assign({}, vm.userProfile, { age: 27, favoriteColor: 'Vue Green' })
```

---
### Filter and Sort
Filter or sort an array without mutating original data
- create a computed property that returns filtered/sorted array

```js
<li v-for="n in evenNumbers">{{ n }}</li>
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

---
### Nested Filtering and Sorting
Suppose your inner loop needs arguments
- use a method
```js
<li v-for="n in even(numbers)">{{ n }}</li>
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

---
### Ranged v-for
Repeats the template that many times
```js
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

---
### v-for with v-if
Can exist on the same node
- v-for has a higher priority
- v-if runs on each iteration of the loop
  - this can be useful

```js
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

---
### v-for with v-if
To conditionally skip execution of the loop
```js
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

---
### v-for with a Component
To use v-for on a custom component
```js
<my-component v-for="item in items" :key="item.id"></my-component>
```
* v-for with a component key is required

---
### Passing data
Components have isolated scopes of their own
- we can use props
```js
<my-component v-for="(item, index) in items" v-bind:item="item"
  v-bind:index="index" v-bind:key="item.id"
></my-component>
```
Injecting item into the component causes tight coupling to v-for
- being explicit about data makes component reusable

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Conditionally rendering elements




