# Computed Properties

---
### Vue Instance
In-template expressions are convenient, but meant for simple operations
Too much logic in templates make them bloated and hard to maintain


For any complex logic, you should use a computed property.

---
### Basic Example

- vm.reversedMessage depends on vm.message
- bindings that depend on vm.reversedMessage are updated when vm.message changes

```js
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
var vm = new Vue({
  el: '#example',
  data: { message: 'Hello' },
  computed: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  }
})
```

---
### Computed Caching vs Methods
We can achieve the same result by invoking a method 
```js
<p>Reversed message: "{{ reverseMessage() }}"</p>
// in component
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```
But computed properties are cached based on their dependencies!

The following computed property will never update
```js
computed: {
  now: function () { return Date.now() }
}
```
A method invocation will always run the function whenever a re-render happens


---
### Watches
- Observe a property
```js
<div id="demo">{{ fullName }}</div>
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

---
### Watch vs Computed properties
In this case computed properties are more efficient
```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

---
### Computed Setter
Computed properties are by default getter-only
You can  provide a setter

```js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

---
### Watchers
- observers for properties
- computed properties are not always the solution
    - fetch data from api based on property change
    - perform an asynchronous operation 
    - limit how often we perform that operation
    - set intermediary states until the final answer 

---
### Watch expression

Instead of declaring, watch imperatively

```js
// keypath
vm.$watch('a.b.c', function (newVal, oldVal) {
  // do something
})

// function
vm.$watch(
  function () {
    // everytime the expression `this.a + this.b` yields a different result,
    // the handler will be called. It's as if we were watching a computed
    // property without defining the computed property itself
    return this.a + this.b
  },
  function (newVal, oldVal) {
    // do something
  }
)

---
### Watch expression

vm.$watch returns an unwatch function
```js
var unwatch = vm.$watch('a', cb)
// later, teardown the watcher
unwatch()
```
To detect nested value changes inside Objects, you need to pass in deep: true 
- you don’t need to do so to listen for Array mutations.
```js
vm.$watch('someObject', callback, {
  deep: true
})
vm.someObject.nestedValue = 123
// callback is fired
```

---
### Option: immediate

Passing in immediate: true in the option will trigger the callback immediately with the current value of the expression:
```js
vm.$watch('a', callback, {
  immediate: true
})
// `callback` is fired immediately with current value of `a`
```
When is this useful?

---

<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Computed vs Methods




