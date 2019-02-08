# Form Input Bindings

---
### Form Input Bindings
v-model directive for two-way data bindings on
- form input
- textarea
- select

v-model will ignore the initial value, checked or selected attributes found on any form elements
- the Vue instance is the source of truth

---
### Basic Example
```js
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
<!-- // Single checkbox, boolean value -->
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```

---
### Array of checkboxes
Bind an array to checkboxes is easy
```js
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>
new Vue({ el: '#example-3', data: { checkedNames: [] } }) 
```

---
### Radio buttons
Binding to radio buttons
```js
<input type="radio" id="one" value="One" v-model="picked">
<label for="one">One</label>
<br>
<input type="radio" id="two" value="Two" v-model="picked">
<label for="two">Two</label>
<br>
<span>Picked: {{ picked }}</span>
```

---
### Selects
Binding to selects and options
```js
<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>

<span>Selected: {{ selected }}</span>
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```

---
### Multiple select (bound to Array)
Bind an array to a select with multiple options
```js
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<br>
<span>Selected: {{ selected }}</span>
```

---
### Dynamically render options
Dynamic options rendered with v-for
```js
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
```

---
### Bind to other values
Checkboxes have a true and false value
```js
<input type="checkbox" v-model="toggle" true-value="yes" false-value="no">
// when checked => vm.toggle === 'yes'
// when unchecked => vm.toggle === 'no'
```
Radio
```js
<input type="radio" v-model="pick" v-bind:value="a">
// when checked => vm.pick === vm.a
```
Select Options
```js
<select v-model="selected">
  <!-- inline object literal -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
// when selected => typeof vm.selected // => 'object'
vm.selected.number // => 123 
```

---
### Modifiers
Modifiers on v-model
- .lazy
  - syncs after change events instead of input event
- .number
  - input automatically typecasted as a number
- .trim
  - trims imput automatically

examples:
```js
<!-- synced after "change" instead of "input" -->
<input v-model.lazy="msg" >
<input v-model.number="age" type="number">
<input v-model.trim="msg">
```
---
### Custom Inputs
You can use v-model on components
Remember that
```js
<input v-model="searchText">
```
is translated to
```js
<input v-bind:value="searchText"  v-on:input="searchText = $event.target.value">
```
On a component, v-model instead does this
```js
<custom-input v-bind:value="searchText" v-on:input="searchText = $event"></custom-input>
```
---
### The component must
Actually, the input inside the component must
- Bind the value attribute to a value prop
- On input, emit own custom input event with new value

```js
Vue.component('custom-input', {
  props: ['value'],
  template: `<input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)">`
})
```
---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Building an input form


<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Building a simple todo app





