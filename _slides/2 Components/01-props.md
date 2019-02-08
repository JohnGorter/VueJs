# Properties

---
### Component properties
Prop Casing (camelCase vs kebab-case)
- HTML attribute names are case-insensitive
- when you’re using in-DOM templates, camelCased prop names need to use their kebab-cased (hyphen-delimited) 

```js
Vue.component('blog-post', {
  // camelCase in JavaScript
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
<!-- kebab-case in HTML -->
<blog-post post-title="hello!"></blog-post>
```
- when using string templates, this limitation does not apply

---
### Prop Types
props can be an array of strings
```js
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```
you can type specify properties
```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object
}
```
This documents your component, and warn users in the console if the wrong type is passed.

---
### Passing Static or Dynamic Props
You can pass properties to your components
```js
<blog-post title="My journey with Vue"></blog-post>
```
or with v-bind
```js
<!-- Dynamically assign the value of a variable -->
<blog-post v-bind:title="post.title"></blog-post>
<!-- Dynamically assign the value of a complex expression -->
<blog-post v-bind:title="post.title + ' by ' + post.author.name"></blog-post>
```
In the two examples above, we pass string values, 
but any type can  be passed to a prop

---
### Passing other types
```js
<!-- Even though `42` is static, we need v-bind to tell Vue that -->
<blog-post v-bind:likes="42"></blog-post>
<!-- Including the prop with no value will imply `true`. -->
<blog-post is-published></blog-post>
<!-- Even though `false` is static, we need v-bind to tell Vue that -->
<blog-post v-bind:is-published="false"></blog-post>
<!-- Even though the array is static, we need v-bind to tell Vue that -->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>
<!-- Even though the object is static, we need v-bind to tell Vue that -->
<blog-post v-bind:author="{name: 'Veronica',company: 'Veridian Dynamics'}"></blog-post>
```
If you want to pass all the properties of an object as props, you can use v-bind without an argument
```js
post: { id: 1, title: 'My Journey with Vue' }
<blog-post v-bind="post"></blog-post>
 - is be equivalent to -
<blog-post v-bind:id="post.id" v-bind:title="post.title"></blog-post>
```

---
### One-Way Data Flow
All props form a one-way-down binding between the child property and the parent one
- when the parent property updates, it will flow down to the child, but not the other way around
- every time the parent component is updated, all props in the child component will be refreshed

Two cases where it’s tempting to mutate a prop:
- prop is used to pass in an initial value
    - the child component wants to use it as a local data property afterwards
    - it’s best to define a local data property that uses the prop as its initial value:
```js
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```
- prop is passed in as a raw value that needs to be transformed
    - it’s best to define a computed property using the prop’s value:
```js
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

- Objects and arrays in JavaScript are passed by reference, so if the prop is an array or object, mutating the object or array itself inside the child component will affect parent state.

---
### Prop Validation
You can specify requirements for its props, such as the types 
- if requirement isn’t met, Vue will warn you in the console.

To specify prop validations, you can provide an object with validation requirements
```js
Vue.component('my-component', {
  props: {
    // Basic type check (`null` matches any type)
    propA: Number,
    // Multiple possible types
    propB: [String, Number],
    // Required string
    propC: { type: String, required: true },
    // Number with a default value
    propD: { type: Number, default: 100 },
    // Object with a default value
    propE: {
      type: Object,
      // Object or array defaults must be returned from
      // a factory function
      default: function () {  return { message: 'hello' } }
    },
    // Custom validator function
    propF: {
      validator: function (value) {
        // The value must match one of these strings
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```
- props are validated before a component instance is created
    - instance properties (e.g. data, computed, etc) will not be available inside default or validator functions

---
### Non-Prop Attributes
An attribute that is passed to a component, but does not have a corresponding prop defined

Components can accept arbitrary attributes, which are added to the component’s root element.

For example, imagine we’re using a 3rd-party bootstrap-date-input component with a Bootstrap plugin that requires a data-date-picker attribute on the input. We can add this attribute to our component instance:

```js
<bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
```
And the data-date-picker="activated" attribute will automatically be added to the root element of bootstrap-date-input.

---
### Replacing/Merging with Existing Attributes
Imagine this is the template for bootstrap-date-input:
```js
<input type="date" class="form-control">
```
To specify a theme for our date picker plugin, we might need to add a specific class, like this:
```js
<bootstrap-date-input data-date-picker="activated" class="date-picker-theme-dark"></bootstrap-date-input>
```
two different values for class are defined:
- form-control, which is set by the component in its template
- date-picker-theme-dark, which is passed to the component by its parent
- the final value: form-control date-picker-theme-dark

---
### Disabling Attribute Inheritance
Set inheritAttrs: false in the component’s options to disable inheritance
```js
Vue.component('my-component', {
  inheritAttrs: false,
  // ...
})
```
Useful in combination with the $attrs instance property, which contains the attribute names and values 

---
### Disabling Attribute Inheritance (2)
Manually decide which element you want to forward attributes to
```js
// pass these properties
{
  required: true,
  placeholder: 'Enter your username'
}
// and use them as...
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  template: `
    <label> {{ label }}
      <input v-bind="$attrs" v-bind:value="value" v-on:input="$emit('input', $event.target.value)">
    </label>
  `
})
```
- inheritAttrs: false option does not affect style and class bindings.

This pattern allows you to use base components more like raw HTML elements, without having to care about the root:
```js
<base-input v-model="username" required placeholder="Enter your username"></base-input>
```


---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Give the component properties








