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
### Writing Your First Plugin
A basic plugin that writes to the console every time a component is mounted to the DOM

```js
// This is your plugin object. It can be exported to be used anywhere.
const MyPlugin = {
  // The install method is all that needs to exist on the plugin object.
  // It takes the global Vue object as well as user-defined options.
  install(Vue, options) {
    // We call Vue.mixin() here to inject functionality into all components.
  	Vue.mixin({
      // Anything added to a mixin will be injected into all components.
      // In this case, the mounted() method runs when the component is added to the DOM.
      mounted() {  console.log('Mounted!'); }
    });
  }
};

export default MyPlugin;
```
Plugin can now be used in a Vue app by importing it and calling Vue.use(MyPlugin)
```js
import Vue from 'vue'
import MyPlugin from './my-vue-plugin.js'
import App from './App.vue'

// The plugin is loaded here.
Vue.use(MyPlugin)
new Vue({
  el: '#app',
  render: h => h(App)
});
```

---
### Adding Capabilities
If you wish to package components or directives to be distributed as a plugin, you might write something like this:
```js
import MyComponent from './components/MyComponent.vue';
import MyDirective from './directives/MyDirective.js';

const MyPlugin {
  install(Vue, options) {
    Vue.component(MyComponent.name, MyComponent)
    Vue.directive(MyDirective.name, MyDirective)
  }
};

export default MyPlugin;
```

---
### Modifying the Global Vue Object
You can modify the global Vue object from a plugin pretty much how you’d expect:

```js
const MyPlugin {
  install(Vue, options) {
    Vue.myAddedProperty = 'Example Property'
    Vue.myAddedMethod = function() {
      return Vue.myAddedProperty
    }
  }
};

export default MyPlugin;
```

---
### Modifying Vue Instances
To add a property or method directly to component instances without any injection mechanism, you can modify the Vue prototype as shown here
```js
const MyPlugin {
  install(Vue, options) {
    Vue.prototype.$myAddedProperty = 'Example Property'
    Vue.prototype.$myAddedMethod = function() {
      return this.$myAddedProperty
    }
  }
};

export default MyPlugin;
```
* properties will now be added to all components and Vue instances.
* Within the Vue community, it is generally expected that plugins which modify the Vue prototype prefix any added properties with a dollar sign ($).

---
### Adding component lifecycle hooks or properties.
You can add lifecycle hooks and make other modifications to Vue components by using a Mixin
```js
const MyPlugin = {
  install(Vue, options) {
    Vue.mixin({
      mounted() {
        console.log('Mounted!');
      }
    });
  }
};

export default MyPlugin;
```

- Mixins are essentially component definitions that get combined into (mixed in) other components.

---
### Automatic Installation
For people who use your plugin outside of a module system, it is often expected that if your plugin is included after the Vue script tag, that it will automatically install itself without the need to call Vue.use(). 

You can implement this by 
```js
// Automatic installation if Vue has been added to the global scope.
if (typeof window !== 'undefined' && window.Vue) {
  window.Vue.use(MyPlugin)
}
```

---
### Distributing your Plugin
Once you’ve written a plugin and are ready to distribute it to the community, here are some common steps for helping people discover your plugin if you’re not already familiar with the process.

- Publish the source code and distributable files to NPM and GitHub. 
  - Make sure you choose a fitting license for your code!
- Open a pull request to the official Vue cool-stuff-discovery repository: Awesome-Vue. 
  - Lots of people come here to look for plugins.
- (Optional) Post about it on the Vue Forum, Vue Gitter Channel and on Twitter with the hashtag #vuejs.


---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Writing a plugin







