# Mixins

---
### Mixins
- a flexible way to distribute reusable functionalities for Vue components
- when a component uses a mixin, all options in the mixin will be “mixed” into the component’s own options

```js
// define a mixin object
var myMixin = {
  created: function () { this.hello() },
  methods: { hello: function () { console.log('hello from mixin!') } }
}

// define a component that uses this mixin
var Component = Vue.extend({
  mixins: [myMixin]
})
```
- to use
```js
var component = new Component() // => "hello from mixin!"
```

---
### Option Merging
When a mixin and the component itself contain overlapping options
- they will be “merged” using appropriate strategies
  - data objects undergo a recursive merge
  - the component’s data takes priority in cases of conflicts

```js
var mixin = {
  data: function () { return {  message: 'hello',  foo: 'abc' } }
}

new Vue({
  mixins: [mixin],
  data: function () { return {  message: 'goodbye', bar: 'def' } },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```
---
### Hook functions 
- functions with the same name are merged into an array so that all of them will be called
- mixin hooks will be called before the component’s own hooks

```js
var mixin = {
  created: function () { console.log('mixin hook called') }
}

new Vue({
  mixins: [mixin],
  created: function () { console.log('component hook called') }
})

// => "mixin hook called"
// => "component hook called"
```

---
### Options
Options that expect object values, for example methods, components and directives, will be merged into the same object
  - the component’s options will take priority when there are conflicting keys in these objects
```js
var mixin = {
  methods: {
    foo: function () { console.log('foo') },
    conflicting: function () { console.log('from mixin') }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () { console.log('bar') },
    conflicting: function () { console.log('from self') }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```

---
### Global Mixin
You can also apply a mixin globally
- once you apply a mixin globally, it will affect every Vue instance created afterwards
- when used properly, this can be used to inject processing logic for custom options:
```js
// inject a handler for `myOption` custom option
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) { console.log(myOption) }
  }
})

new Vue({ myOption: 'hello!' })
// => "hello!"
```

---
### Custom Option Merge Strategies
- when custom options are merged, they use the default strategy which overwrites the existing value
- if a custom option merge has custom logic, you need to attach a function to Vue.config.optionMergeStrategies

```js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) { // return mergedVal }
```

For most object-based options, you can use the same strategy used by methods:
```js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```

---
### Complex merging
A more advanced example can be found on Vuex‘s 1.x merging strategy:
```js
const merge = Vue.config.optionMergeStrategies.computed
Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
  if (!toVal) return fromVal
  if (!fromVal) return toVal
  return {
    getters: merge(toVal.getters, fromVal.getters),
    state: merge(toVal.state, fromVal.state),
    actions: merge(toVal.actions, fromVal.actions)
  }
}
```
