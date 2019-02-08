# Custom Events

---
### Event Names
In contrast to components and props
- event names don’t provide any automatic case transformation. 
  - the name of an emitted event must exactly match the name used to listen to that event
- event names will never be used as variable/property names in JS, no reason to use camelCase or PascalCase
- v-on event listeners inside DOM templates will be automatically transformed to lowercase (due to HTML’s case-insensitivity)

```js
this.$emit('myEvent')
// Listening to the kebab-cased version will have no effect:
<my-component v-on:my-event="doSomething"></my-component>
```
- always use kebab-case for event names.

---
### Customizing Component v-model
V-model on a component uses 
- value as the prop 
- input as the event

some input types such as checkboxes and radio buttons use checked
using the model option can avoid a conflict in such cases
```js
Vue.component('base-checkbox', {
  model: { prop: 'checked', event: 'change' },
  props: { checked: Boolean },
  template: `
    <input  type="checkbox" v-bind:checked="checked" v-on:change="$emit('change', $event.target.checked)">
  `
})

// use it
<base-checkbox v-model="lovingVue"></base-checkbox>
```

---
### Binding Native Events to Components
To listen directly to a native event on the root element of a component, use the .native modifier for v-on
```js
<base-input v-on:focus.native="onFocus"></base-input>
```
Be aware when base-input refactors to
```js
<label>
  {{ label }}
  <input  v-bind="$attrs" v-bind:value="value" v-on:input="$emit('input', $event.target.value)">
</label>
```

The .native listener in the parent would silently break
- no errors,  the onFocus handler wouldn’t be called

---
### Binding Native Events to Components (2)
To solve this problem, there is a $listeners property containing an object of listeners being used on the component
```js
{
  focus: function (event) { /* ... */ }
  input: function (value) { /* ... */ },
}
```
Using the $listeners property,  all event listeners can be forwarded to a specific child element with v-on="$listeners"
```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  computed: {
    inputListeners: function () {
      var vm = this
      // `Object.assign` merges objects together to form a new object
      return Object.assign({},
        // We add all the listeners from the parent
        this.$listeners,
        // Then we can add custom listeners or override the
        // behavior of some listeners.
        {
          // This ensures that the component works with v-model
          input: function (event) { vm.$emit('input', event.target.value)}
        }
      )
    }
  },
  template: `
    <label>{{ label }}<input v-bind="$attrs" v-bind:value="value" v-on="inputListeners"></label>
  `
})
```

- the '<base-input>' component is a fully transparent wrapper

---
### Two way databinding
Two way data binding can be done using .sync Modifier
- emitting events in the pattern of update:myPropName
```js
this.$emit('update:title', newTitle)
```
- parent can listen to that event and update a local data property
```js
<text-document v-bind:title="doc.title" v-on:update:title="doc.title = $event"></text-document>
// short-hand
<text-document v-bind:title.sync="doc.title"></text-document>
// also works with objects
<text-document v-bind.sync="doc"></text-document>
// This passes each property in the doc object (e.g. title) as an individual prop, then adds v-on update listeners for each one.
```

v-bind with the .sync modifier does not work 
- with expressions (e.g. v-bind:title.sync=”doc.title + ‘!’” is invalid)
- with a literal object, such as in v-bind.sync=”{ title: doc.title }”

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Custom and native events








