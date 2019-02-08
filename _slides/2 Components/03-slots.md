# Slots

---
### Slots
Placeholders to fill content from parent
- provide flexibility
- inspired from Web Components Standards
  - Shadow Dom v1
- can have names to provide multiple placeholders

---
### Slot Content
Components can be composed
```js
<navigation-link url="/profile">
  Your Profile
</navigation-link>
```
Then in the template for '<navigation-link>'
```js
<a v-bind:href="url" class="nav-link">
  <slot></slot>
</a>
```
- when the component renders, '<slot></slot>' will be replaced by “Your Profile”

---
### Slot Content (2)
slots can contain HTML

```js
<navigation-link url="/profile">
  <!-- Add a Font Awesome icon -->
  <span class="fa fa-user"></span>
  Your Profile
</navigation-link>
```
Or even other components

```js
<navigation-link url="/profile">
  <!-- Use a component to add an icon -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Your Profile
</navigation-link>
```

- if '<navigation-link>'‘s template did not contain a '<slot>' element, content provided between tags would be discarded.

---
### Compilation Scope
slot has access to the same instance properties (i.e. the same “scope”) as the rest of the template
```js
<navigation-link url="/profile"> Logged in as {{ user.name }}</navigation-link>
```
slot does not have access to '<navigation-link>'‘s scope. Trying to access url would not work:

```js
<navigation-link url="/profile">
  Clicking here will send you to: {{ url }}
  <!-- The `url` will be undefined, because this content is passed _to_ <navigation-link>, rather than defined _inside_ <navigation-link> component. -->
</navigation-link>
```
Everything in the parent template is compiled in parent scope; everything in the child template is compiled in the child scope

---
### Fallback Content
There are cases when it’s useful to specify fallback (i.e. default) content for a slot, to be be rendered only when no content is provided:
```js
<button type="submit"><slot></slot></button>
// we might want the text “Submit” to be rendered inside the <button> most of the time
<button type="submit"><slot>Submit</slot></button>

// now when we use <submit-button> in a parent component, providing no content for the slot:
<submit-button></submit-button>
// will render the fallback content, “Submit”:
// but if we provide content:
<submit-button> Save </submit-button>
// then the provided content will be rendered instead
```

---
### Named Slots
There are times when it’s useful to have multiple slots
- the slot element has a special attribute, name, which can be used to define slots
```js
<div class="container">
  <header><slot name="header"></slot></header>
  <main><slot></slot></main>
  <footer><slot name="footer"></slot></footer>
</div>
```
A slot outlet without name implicitly has the name “default”.


---
### Named Slots (2)
To provide content to named slots, we can use the v-slot directive on a template, 
- provide the name of the slot as v-slot‘s argument
```js
<base-layout>
  <template v-slot:header><h1>Here might be a page title</h1></template>
  <p>A paragraph for the main content.</p>
  <p>And another one.</p>
  <template v-slot:footer><p>Here's some contact info</p></template>
</base-layout>
```

- everything inside the template elements will be passed to the corresponding slots
- any content not wrapped is assumed to be for the default slot
- template v-slot:default can be used to be more specific
- v-slot can only be added to a template, unlike the deprecated slot attribute.

---
### Scoped Slots
To make data available to the slot content in the parent, we can bind this data as an attribute to the slot element
```js
// inside the current-user element
<span> 
  <slot v-bind:user="user"> {{ user.lastName }}</slot>
</span>
```
- attributes bound to a slot element are called slot props
- in the parent scope, we can use v-slot with a value to define a name for the slot props we’ve been provided:
```js
// use the user property from the outside
<current-user>
  <template v-slot:default="slotProps"> {{ slotProps.user.firstName }} </template>
</current-user>
```
- slotProps is a chosen name

---
### Abbreviated Syntax for Lone Default Slots
When only the default slot is provided content
- the component’s tags can be used as the slot’s template

```js
<current-user v-slot:default="slotProps">{{ slotProps.user.firstName }}</current-user>
// -or even shorter -
<current-user v-slot="slotProps">{{ slotProps.user.firstName }}</current-user>
```
- the abbreviated syntax for default slot cannot be mixed with named slots
```js
<!-- INVALID, will result in warning -->
<current-user v-slot="slotProps"> 
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps"> slotProps is NOT available here </template>
</current-user>
```
- whenever there are multiple slots, use the full template based syntax for all slots!

---
### Destructuring Slot Props
Internally, scoped slots work by wrapping your slot content in a function passed a single argument:
```js
function (slotProps) { // ... slot content ... }
```
- the value of v-slot can accept any valid JS expression that can appear in the argument of a function definition. 
- you can use ES2015 destructuring to pull out specific slot props
```js
<current-user v-slot="{ user }">  {{ user.firstName }}</current-user>
```
- this can make the template much cleaner
- it opens other possibilities, such as renaming props, e.g. user to person
```js
<current-user v-slot="{ user: person }"> {{ person.firstName }} </current-user>
```
- you can even define fallbacks, to be used in case a slot prop is undefined:
```js
<current-user v-slot="{ user = { firstName: 'Guest' } }"> {{ user.firstName }}</current-user>
```


---
### Dynamic directive arguments
Dynamic directive arguments also work on v-slot, 
allowing the definition of dynamic slot names:
```js
<base-layout>
  <template v-slot:[dynamicSlotName]></template>
</base-layout>
```

---
### Named Slots Shorthand
Similar to v-on and v-bind, v-slot also has a shorthand
```js
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>
  <p>A paragraph for the main content.</p>
  <p>And another one.</p>
  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```
- the shorthand is only available when an argument is provided. The following syntax is invalid
```js
<current-user #="{ user }">
  {{ user.firstName }}
</current-user>
```
You must always specify the name of the slot for the shorthand

<current-user #default="{ user }">
  {{ user.firstName }}
</current-user>

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








