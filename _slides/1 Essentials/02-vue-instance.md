# The Vue Instance

---
### Vue Instance
Every Vue application starts by creating a new Vue instance with the Vue function:

var vm = new Vue({
  // options
})

* this is called the root instance

---
### Root instance 
A component tree could look like
```js
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

---
### Data and Methods

Data is added and monitored by Vue

```js
var data = { a: 1 }
var vm = new Vue({ el: '#example', data: data })

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true
// $watch is an instance method
vm.$watch('a', function (newValue, oldValue) {
  // This callback will be called when `vm.a` changes
})

vm.b = 'john' // => this does not update anything
```

---
### Lifecycle hooks
Each Vue instance goes through series of steps when created
It needs to 
- set up data observation
- compile the template 
- mount the instance to the DOM
- update the DOM when data changes

Along the way, it runs functions called lifecycle hooks
All hooks are called with this context pointing to Vue instance

---
### Lifecycle hooks
- beforeCreate, created, 
- beforeMount, mounted, 
- beforeUpdate, updated, 
- beforeDestroy, destroyed

<div style="float:right;position:absolute;right:50px;top:-200px;height:15vh;width:28%;">
<img src="images/lifecycle.png" style="max-height:none;" />
</div>

---
### Gotcha's 
- Don’t use arrow functions on an options property or callback
```js 
created: () => console.log(this.a) 
vm.$watch('a', newValue => this.myMethod())
```

Arrow functions are bound to the parent context, not the Vue instance!



