# Custom Directives

---
### Custom Directives
you can register your own custom directives
- the primary form of code reuse and abstraction is components 
- however there may be cases where you need some low-level DOM access on plain elements
  - this is where custom directives would still be useful
  - for example would be focusing on an input element

```js
// Register a global custom directive called `v-focus`
Vue.directive('focus', {
  // When the bound element is inserted into the DOM...
  inserted: function (el) { el.focus() }
})
```
If you want to register a directive locally instead, components also accept a directives option:
```js
directives: {
  focus: {
    // directive definition
    inserted: function (el) { el.focus() }
  }
}
```
- in a template, you can use the new v-focus attribute on any element
```js
<input v-focus>
```

---
### Hook Functions
A directive definition object can provide several hook functions (all optional)
- bind: called only once, when the directive is first bound to the element
  - this is where you can do one-time setup work
- inserted: called when the bound element has been inserted into its parent node 
  - this only guarantees parent node presence, not necessarily in-document
- update: called after the containing component’s VNode has updated
  - possibly before its children have updated
- componentUpdated: called after the containing component’s VNode and the VNodes of its children have updated.
- unbind: called only once, when the directive is unbound from the element

---
### Directive Hook Arguments
Directive hooks are passed these arguments:

- el: The element of the directive 
- binding: An object containing:
  - name: The name of the directive, without the v- prefix
  - value: The value passed to the directive. For example in v-my-directive="1 + 1", the value would be 2
  - oldValue: The previous value, only available in update and componentUpdated. It is available whether or not the value has changed.
  - expression: The expression of the binding as a string. For example in v-my-directive="1 + 1", the expression would be "1 + 1".
  - arg: The argument passed to the directive, if any. For example in v-my-directive:foo, the arg would be "foo".
  - modifiers: An object containing modifiers, if any. For example in v-my-directive.foo.bar, the modifiers object would be { foo: true, bar: true }.
- vnode: The virtual node produced by Vue’s compiler. See the VNode API for full details.
- oldVnode: The previous virtual node, only available in the update and componentUpdated hooks.

Apart from el, you should treat these arguments as read-only and never modify them. If you need to share information across hooks, it is recommended to do so through element’s dataset.

---
### Example
An example of a custom directive using some of these properties:
```js
<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})

new Vue({
  el: '#hook-arguments-example',
  data: { message: 'hello!'}
})
```

---
### Function Shorthand
In many cases, you may want the same behavior on bind and update, but don’t care about the other hooks. For example:
```js
// this function is used for bind and update
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

---
### Object Literals
If your directive needs multiple values, you can also pass in a JavaScript object literal
- Remember, directives can take any valid JavaScript expression
```js
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```


---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Create a custom directive








