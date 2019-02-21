# Advanced Routing

---
### Navigation Guards
Primarily used to guard navigations 
- by redirecting it 
- by canceling it

Hook into the route navigation process, by
 - globally
 - per-route
 - in-component

* Remember params or query changes won't trigger enter/leave navigation guards  
  - watch the $route object to react to those changes
  - use the beforeRouteUpdate in-component guard

---
### Global Before Guards
Register global before guards using router.beforeEach
```js
const router = new VueRouter({ ... })
router.beforeEach((to, from, next) => {
  // ...
})
```

Global before guards are called in creation order

---
### Global Before Guards (2)
Every guard function receives three arguments:
- to: Route: target Route Object being navigated to.
- from: Route: current route being navigated away from
- next: Function: must be called to resolve the hook

The action depends on the arguments provided to next
- next(): move on to the next hook in the pipeline
- If no hooks are left, the navigation is confirmed
- next(false): abort the current navigation
- next('/') or next({ path: '/' }): redirect to different location
- next(error): (2.4.0+) if argument is an Error, navigation will be aborted and error will be passed to callbacks registered via router.onError()

* Make sure to always call the next function, otherwise the hook will never be resolved.

---
### Global Resolve Guards
Register a global guard with router.beforeResolve 
- similar to router.beforeEach

Difference 
- resolve guards will be called right before the navigation is confirmed,
- after all in-component guards and async route components are resolved

---
### Global After Hooks
Register global after hooks

```js
router.afterEach((to, from) => {
  // ...
})
```

Unlike guards, hooks do not get a next function thus cannot affect  navigation

---
### Per-Route Guard
Define beforeEnter guards directly on a route's configuration object
```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```
These guards have the exact same signature as global before guards.

---
### In-Component Guards
Directly define route navigation guards inside route components 

```js
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // called before the route that renders this component is confirmed, does NOT have access to `this` component instance
  },
  beforeRouteUpdate (to, from, next) {
    // called when the route that renders this component has changed but this component is reused in the new route.
  },
  beforeRouteLeave (to, from, next) {
    // called when the route that renders this component is about to be navigated away from.
  }
}
```

---
### In-Component Guards (2)
BeforeRouteEnter guard does NOT have access to this, because the guard is called before the navigation is confirmed, thus the new entering component has not even been created yet

However you can access the instance by passing a callback to next

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // access to component instance via `vm`
  })
}
```

* beforeRouteEnter is the only guard that supports passing a callback to next

---
### In-Component Guards (3)
The leave guard is usually used to prevent the user from accidentally leaving the route with unsaved edits. 
The navigation can be canceled by calling next(false).

```js
beforeRouteLeave (to, from, next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

---
### Summary
The Full Navigation Resolution Flow
- Navigation triggered
- Call leave guards in deactivated components
- Call global beforeEach guards
- Call beforeRouteUpdate guards in reused components
- Call beforeEnter in route configs
- Resolve async route components
- Call beforeRouteEnter in activated components
- Call global beforeResolve guards
- Navigation confirmed
- Call global afterEach hooks
- DOM updates triggered
- Call callbacks passed to next in beforeRouteEnter guards with instantiated instances

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Navigation Guards

---
### Meta Fields
You can include a meta field when defining a route
```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      children: [
        {
          path: 'bar',
          component: Bar,
          meta: { requiresAuth: true }
        }
      ]
    }
  ]
})
```
---
### Meta Fields (2)
Checking the Meta Field goes like
```js
router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.requiresAuth)) {
    // this route requires auth, check if logged in
    // if not, redirect to login page.
    if (!auth.loggedIn()) {
      next({
        path: '/login',
        query: { redirect: to.fullPath }
      })
    } else { next()  }
  } else { next() }
})
```


---
### Transitions
Since the router-view is essentially a dynamic component, we can apply transition effects to it the same way using the transition component
```js
<transition>
  <router-view></router-view>
</transition>
```

---
### Per-Route Transition
For each route's component to have different transitions, you can use transition with different names inside each route component
```js
const Foo = {
  template: `
    <transition name="slide">
      <div class="foo">...</div>
    </transition>
  `
}
const Bar = {
  template: `
    <transition name="fade">
      <div class="bar">...</div>
    </transition>
  `
}
```

---
### Route-Based Dynamic Transition
Determine the transition to use dynamically based on the relationship between the target route and current route
```js
<!-- use a dynamic transition name -->
<transition :name="transitionName">
  <router-view></router-view>
</transition>
// then, in the parent component,
// watch the `$route` to determine the transition to use
watch: {
  '$route' (to, from) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
  }
}
```

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Transitions

---
### Data Fetching
Fetch data from the server when a route is activated
- example: before rendering a user profile, fetch data from  server

Archieve in two different ways
- Fetching After Navigation
  - perform the navigation first
  - fetch data in the incoming component's lifecycle hook
  - display a loading state while data is being fetched.
- Fetching Before Navigation
  - fetch data before navigation in the route enter guard
  - perform the navigation after data has been fetched

* choice depends on the user experience you are aiming for

---
### Fetching After Navigation
Navigate and render the incoming component immediately
- fetch data in the component's created hook

Gives us the opportunity to display a loading state while the data is being fetched over the network

```js
<template>
  <div class="post">
    <div class="loading" v-if="loading"> Loading... </div>
    <div v-if="error" class="error">  {{ error }} </div>
    <div v-if="post" class="content">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>
export default {
  data () {
    return { loading: false, post: null, error: null }
  },
  created () {  this.fetchData() },
  watch: { '$route': 'fetchData' },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) { this.error = err.toString()
        } else { this.post = post }
      })
    }
  }
}
```

---
### Fetching Before Navigation
Fetch the data before navigating to the new route
```js
export default {
  data () { return { post: null, error: null }},
  beforeRouteEnter (to, from, next) {
    getPost(to.params.id, (err, post) => {
      next(vm => vm.setData(err, post))
    })
  },
  beforeRouteUpdate (to, from, next) {
    this.post = null
    getPost(to.params.id, (err, post) => {
      this.setData(err, post)
      next()
    })
  },
  methods: {
    setData (err, post) {
      if (err) { this.error = err.toString() } 
      else { this.post = post }
    }
  }
}
```
* User will stay on the previous view while the resource is being fetched for the incoming view

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Data Fetching

---
### Scroll Behavior
When using client-side routing, we may want to scroll to top when navigating to a new route, or preserve the scrolling position of history entries
- scrollBehavior function
```js
const router = new VueRouter({
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // return desired position
  }
})
```
* this feature only works if the browser supports history.pushState.

---
### scrollBehavior function
This function receives the to and from route objects
- Third argument (savedPosition) is only available if this is a popstate navigation (triggered by the browser's back/forward buttons)

The function can return a scroll position object. The object could be in the form of:
```js
{ x: number, y: number }
{ selector: string, offset? : { x: number, y: number }} 
// (offset only supported in 2.6.0+)
```

* If a falsy value or an empty object is returned, no scrolling will happen.


---
### scrollBehavior function examples
```js
// Simply scroll the page to top for all route navigations
scrollBehavior (to, from, savedPosition) {
  return { x: 0, y: 0 }
}
```

Returning the savedPosition will result in a native-like behavior when navigating with back/forward buttons:
```js
scrollBehavior (to, from, savedPosition) {
  if (savedPosition) { return savedPosition
  } else { return { x: 0, y: 0 } }
}
```


---
### scrollBehavior function examples (2)
If you want to simulate the "scroll to anchor" behavior:
```js
scrollBehavior (to, from, savedPosition) {
  if (to.hash) {
    return {
      selector: to.hash
      // , offset: { x: 0, y: 10 }
    }
  }
}
```

* You can also use route meta fields to implement fine-grained scroll behavior control

---
### Async Scrolling 
You can also return a Promise that resolves to the desired position descriptor:
```js
scrollBehavior (to, from, savedPosition) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({ x: 0, y: 0 })
    }, 500)
  })
}
```
* It's possible to hook this up with events from a page-level transition component

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Scrolling


---
### Lazy Loading Routes
When building apps with a bundler
- the JavaScript bundle can become quite large
  - affect the page load time
Solution
- only load components when the route is visited

First, define an async component as a factory function that returns a Promise 
```js
const Foo = () => Promise.resolve({ /* component definition */ })
```

Second, in webpack 2, use dynamic import syntax to indicate a code-split point
```js
import('./Foo.vue') // returns a Promise
```

* if you are using Babel, you will need to add the syntax-dynamic-import plugin 

---
### Lazy Loading Routes (2)
Combining the two, this is how to define an async component that will be automatically code-split by webpack:
```js
const Foo = () => import('./Foo.vue')
```
Nothing needs to change in the route config
```js
const router = new VueRouter({
  routes: [
    { path: '/foo', component: Foo }
  ]
})
```

---
### Grouping Components in the Same Chunk
Sometimes we may want to group all the components nested under the same route into the same async chunk. 

```js
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
```

* webpack will group any async module with the same chunk name into the same async chunk.

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Lazy Loading Routes

---

<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Implement Routing