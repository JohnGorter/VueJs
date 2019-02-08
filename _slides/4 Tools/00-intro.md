# Tools

---
### Single File Components
Global components will be defined using Vue.component followed by new Vue({ el: '#container' }) 
to target a container element in the body of every page

Works for small to medium-sized projects
disadvantages:

- global definitions force unique names for every component
- string templates lack syntax highlighting and require ugly slashes for multiline HTML
- no CSS support means that while HTML and JavaScript are modularized into components, CSS is left out
- no build step restricts us to HTML and ES5JavaScript, rather than preprocessors like Pug and Babel

---
### Single File Components (2)
All of these are solved by single-file components 
- have a .vue extension
- build tools such as Webpack or Browserify are required

Here’s an example
```js
 File: Hello.vue
<template> <p>{{ greeting }} World!</p></template>
<script>
module.exports = {
  data: function () { return { greeting: 'Hello' } }
}
</script>
<style scoped>
p { font-size: 2em; text-align: center; }
</style>
```

In this file we get
- Complete syntax highlighting
- CommonJS modules
- Component-scoped CSS
- Use of preprocessors such as Pug, Babel 

---
### Single File Components (3)
Example of jade and stylus preprocessors:
```js
 Hello.vue
<template lang="jade">
div
  p {{ greeting }} World!
  OtherComponent
</template>

<script>
import OtherComponent from './OtherComponent.vue'
export default {
  components: {
    OtherComponent
  },
  data () { return { greeting: 'Hello' } }
}
</script>
<style lang="stylus" scoped>
p
  font-size 2em
  text-align center
</style>
```

---
### What About Separation of Concerns?
Separation of concerns is not equal to separation of file types
In modern UI development
- no dividing the codebase into three huge layers that interweave with one another 
 - divide into loosely-coupled components and compose them

Inside a component, its template, logic and styles are inherently coupled, and collocating them actually makes the component more cohesive and maintainable.

---
### What About Separation of Concerns? (2)
You can separate your JavaScript and CSS into separate files:
```js
<!-- my-component.vue -->
<template>
  <div>This will be pre-compiled</div>
</template>
<script src="./my-component.js"></script>
<style src="./my-component.css"></style>
```


---

<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Building a simple todo app