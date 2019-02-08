# Dynamic & Async Components


---
### keep-alive with Dynamic Components
- when switching between  components, you’ll sometimes want to maintain their state 
- each time you switch to a new component, Vue creates a new instance
- normally useful behavior, but sometimes component instances need to be cached 
- 'keep-alive' element
```js
<!-- Inactive components will be cached! -->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>
```

---
### Async Components
- load a component from the server when it’s needed
- define your component as a factory function that asynchronously resolves your component definition

Vue will only trigger the factory function when the component needs to be rendered and will cache the result for future re-renders

```js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // Pass the component definition to the resolve callback
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

---
### Async Components (2)

- you also could use async components together with Webpack’s code-splitting feature
```js
Vue.component('async-webpack-example', function (resolve) {
  // This special require syntax will instruct Webpack to
  // automatically split your built code into bundles which
  // are loaded over Ajax requests.
  require(['./my-async-component'], resolve)
})
```

---
### Async Components (3)

- you can also return a Promise in the factory function, so with Webpack 2 and ES2015 syntax
```js
Vue.component(
  'async-webpack-example',
  // The `import` function returns a Promise.
  () => import('./my-async-component')
)
```
or when using local registration
```js
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```

---
### andling Loading State
- the async component factory can also return an object of the following format:
```js
const AsyncComponent = () => ({
  // The component to load (should be a Promise)
  component: import('./MyComponent.vue'),
  // A component to use while the async component is loading
  loading: LoadingComponent,
  // A component to use if the load fails
  error: ErrorComponent,
  // Delay before showing the loading component. Default: 200ms.
  delay: 200,
  // The error component will be displayed if a timeout is
  // provided and exceeded. Default: Infinity.
  timeout: 3000
})
```

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Async components








