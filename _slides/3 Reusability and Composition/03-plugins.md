# Plugins

---
### Plugins
- usually add global-level functionality to Vue
- there is no strictly defined scope for a plugin 
- there are typically several types of plugins:
  - add some global methods or properties. e.g. vue-custom-element
  - add one or more global assets: directives/filters/transitions etc. e.g. vue-touch
  - add some component options by global mixin. e.g. vue-router
  - add some Vue instance methods by attaching them to Vue.prototype.
  - a library that provides an API of its own while at the same time injecting some combination of the above. e.g. vue-router

---
### Using a Plugin
Use plugins by calling the Vue.use() global method
- this has to be done before you start your app by calling new Vue():
```js
// calls `MyPlugin.install(Vue)`
Vue.use(MyPlugin)
new Vue({
  //... options
})
```
You can optionally pass in some options:
```js
Vue.use(MyPlugin, { someOption: true })
```
Vue.use automatically prevents you from using the same plugin more than once
- calling it multiple times on  same plugin will install it only once

---
### Using a Plugin (2)
Some plugins such as vue-router automatically calls Vue.use() 
- if Vue is available as a global variable 

In a module environment such as CommonJS, call Vue.use() explicitly:
```js
// When using CommonJS via Browserify or Webpack
var Vue = require('vue')
var VueRouter = require('vue-router')

// Don't forget to call this
Vue.use(VueRouter)
```

Checkout awesome-vue for a huge collection of community-contributed plugins and libraries.

---
### Writing a Plugin
A Vue.js plugin should expose an install method
- the method will be called with the Vue constructor as the first argument, along with possible options
```js
MyPlugin.install = function (Vue, options) {
  // 1. add global method or property
  Vue.myGlobalMethod = function () {}

  // 2. add a global asset
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {}
    ...
  })

  // 3. inject some component options
  Vue.mixin({
    created: function () {}
    ...
  })

  // 4. add an instance method
  Vue.prototype.$myMethod = function (methodOptions) {}
}
```

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Writing a plugin








