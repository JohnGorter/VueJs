# Routing

---
### Routing concepts
- Routing
- Dynamic Routes
- Nested Routes
- Programmatic Navigation
- Named Routes
- Named Views
- Redirects and Aliasses
- Props
- HTML5 History mode

---
### Advanced Routing
- Navigation Guards
- Route Meta Fields
- Transitions
- Data Fetching
- Scroll behavior
- Lazy Loading Routes


---
### Routing
Routing is
- mapping a location to an application state
- usually maps from url to component

Routing provides
- working back and forward button support in browser
- deeplink sharing


---
### Routing in VueJs

vue-router
- from cdn: https://unpkg/com/vue-router
- from npm: npm i vue-router

---
### Routing in VueJS
- Plugin
    - Router
- Components 
    - router-link
    - router-view
- Instance fields
    - $route
    - $router



---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Hello Routing

---
### Dynamic Routes
Suppose we want a user detail screen
```js
const User = { template: '<div>User</div>' }
const router = new VueRouter({
  routes: [
    // dynamic segments start with a colon
    { path: '/user/:id', component: User }
  ]
})
// URLs like /user/foo and /user/bar will map to same route
```

- dynamic segment colon :
- exposed as this.$route.params 

```js
const User = { template: '<div>User {{ $route.params.id }}</div>' }
```

---
### Route pattern match
The $route object exposes information like

|pattern|matched path|$route.params|
|---|---|---|
|/user/:username|/user/evan|{ username: 'evan' }|
|/user/:username/post/:post_id|/user/evan/post/123| { username: 'evan', post_id: '123' } |

$route also has the following properties
- query, hash, path, fullPath, matched, name, redirectedFrom

---
### Reacting to Params Changes
- Switching from similar routes could happen
    - user/1 becomes user/2
- The same component is reused so lifecycle hooks are not called
- Use watch the $route object
```js
const User = {
  template: '...',
  watch: { '$route' (to, from) {}}
}
```
- Use beforeRouteUpdate navigation guard
```js
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {}
}
```

---
### The Asterisk (*)
- Catch all and 404 Not found Route
    - matches all remaining characters
    - usually used to 404 client side

```js
// will match everything
{ path: '*' }
// will match anything starting with `/user-`
{ path: '/user-*' }
```
- ordering of the routes does matter
    - asterisk routes at the end

---
### The Asterisk (*) (2)
- $route.params.pathMatch automatically added 
    - contains the rest of the url matched by asterisk
```js
// Given a route { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// Given a route { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```

---
### Advanced Matching Patterns
vue-router uses path-to-regexp as its path matching engine
- support for many advanced matching patterns
    - optional dynamic segments
    - zero or more / one or more requirements
    - custom regex patterns
---
### Matching Priority
When multiple routes match
- priority is determined by the order of route definition
- the earlier a route is defined, the higher priority it gets.

---
### Nested Routes
- components can be nested multiple levels deep
- segments of a URL corresponds to a certain structure of nested components
```js
/user/foo/profile // => profile component in user component    
/user/foo/posts // => post component in user component    
```

```js
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        { // UserProfile will be rendered inside User's  <router-view>  when /user/:id/profile is matched
          path: 'profile', component: UserProfile
        },
        { // UserPosts will be rendered inside User's   <router-view>  when /user/:id/posts is matched
          path: 'posts', component: UserPosts
        }
      ]
    }
  ]
})
```

---
### Programmatic Navigation

- alternative to router-link
- router.push(location, onComplete?, onAbort?)

|Declarative|Programmatic|
|---|---|
|router-link :to="..."|router.push(...)|


Examples
```js
// literal string path
router.push('home')
// object
router.push({ path: 'home' })
// named route
router.push({ name: 'user', params: { userId: '123' } })
// with query, resulting in /register?plan=private
router.push({ path: 'register', query: { plan: 'private' } })
```

---
### Programmatic Navigation Restrictions
- params are ignored if a path is provided
```js
const userId = '123'
router.push({ name: 'user', params: { userId } }) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// This will NOT work
router.push({ path: '/user', params: { userId } }) // -> /user
```

- The same rules apply for the to property of the router-link component.
- If the destination is the same as the current route and only params are changing
  - going from /users/1 -> /users/2
  - use beforeRouteUpdate to react to changes 

---
### Router Replace
router.replace(location, onComplete?, onAbort?)
- acts like router.push
- difference: it navigates without pushing a new history entry


|Declarative|Programmatic|
|---|---|
|router-link :to="..." replace|router.replace(...)|

---
### Router Go
router.go(n)
- move n many steps to go forwards or go backwards in the history stack
- similar to window.history.go(n).

Examples
```js
// go forward by one record, the same as history.forward()
router.go(1)
// go back by one record, the same as history.back()
router.go(-1)
// go forward by 3 records
router.go(3)
//  fails silently if there aren't that many records.
router.go(-100)
router.go(100)
```

---
### Summary

|Browser API| VueJS|
|---|---|
|router.push|window.history.pushState|
|router.replace|window.history.replaceState|
|router.go|window.history.go|

---

<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Routing and Components

---

<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Implement Routing