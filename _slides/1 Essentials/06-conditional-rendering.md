# Conditional Rendering

---
### V-If, V-Else, V-Else-If
These directives render DOM nodes conditionally
- expression results in true/false
- node gets recreated each time the expression is reevaluated
 

```js
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

---
### Conditional Groups 
Template with v-if on <template>
- What if we want to toggle more than one element?
```js
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

---
### Reusable Elements and Keys
- render elements as efficiently as possible
  - re-using instead of rendering from scratch
  
example:
```js
<template v-if="loginType === 'username'">
  <input placeholder="Enter your username">
</template>
<template v-else>
  <input placeholder="Enter your email address">
</template>
```
Switching the loginType, the '<input>' is not replaced - just its placeholder
User input is kept, this can be annoying
These two elements are completely separate - don’t re-use them.” 
  - add keys

```js
<template v-if="loginType === 'username'">
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <input placeholder="Enter your email address" key="email-input">
</template>
```
Now those inputs will be rendered from scratch each time you toggle

---
### V-Show
- conditionally displaying an element 
```js
<h1 v-show="ok">Hello!</h1>
```
- always be rendered and remains in the DOM
- toggles the display CSS property of the element
- doesn’t support the '<template>' element,
- doesn't work with v-else

---
### v-if vs v-show
v-if 
- “real” conditional rendering 
  - ensures that event listeners and child components are destroyed and re-created
  - is lazy
    - if condition is false on initial render, it will not do anything
v-show 
- always renders regardless, with CSS-based toggling

Generally speaking, v-if has higher toggle costs while v-show has higher initial render costs


<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Conditionally rendering elements




