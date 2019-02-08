# Class and Style Bindings

---
### Class and Style Bindings
class list and its inline styles. 
- attributes, we can use v-bind to handle them
  - string concatenation is annoying and error-prone

Special enhancements when v-bind is used with class and style
- also expression evaluation for objects or arrays

---
### Object Syntax
Pass an object to v-bind:class 
```js
<div v-bind:class="{ active: isActive }"></div>
```
Multiple classes toggled by having more fields in the object 
and co-existence with the plain class attribute
```js
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>

data: {
  isActive: true,
  hasError: false
}

// renders:
<div class="static active"></div>
```

---
### Computed property
This is a common and powerful pattern:
```js
<div v-bind:class="classObject"></div>
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

---
### Array Syntax
We can pass an array to v-bind:class 
```js
<div v-bind:class="[activeClass, errorClass]"></div>
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
// renders:
<div class="active text-danger"></div>
```

Or with a ternary expression:
```js
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```
Always apply errorClass, but only activeClass when isActive is truthy

Object syntax inside array syntax:
```js
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

---
### With Components

Class attribute on custom component results in 
classes added to component’s root element
Existing classes on element will remain
```js
// if you declare this component
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
// And add  classes when using it:
<my-component class="baz boo"></my-component>
// The rendered HTML will be:
<p class="foo bar baz boo">Hi</p>
```

---
### Binding Inline Styles
Object syntax for v-bind:style is straightforward 
- it looks almost like CSS, except it’s a JavaScript object
```js
<div v-bind:style="styleObject"></div>

data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

---
### Other Syntaxis
Array Syntax
```js
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

Vue will automatically add appropriate prefixes to applied styles.
Starting in 2.3.0+ an array of multiple (prefixed) values can be provided
```js
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
// In this example, it will render display: flex 
```
This will only render the last value in the array which the browser supports

---

<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Sttyling your application




