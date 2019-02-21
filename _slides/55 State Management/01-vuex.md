# VueX

---
### Vuex Concepts

Data Architecture based on Redux/Flux
- Actions
- Dispatcher
- Store
- View

<img src="/images/flux.jpg" />

Vuex is VueJS implementation of Redux/Flux

---
### VueX State Management

> Vuex is a state management pattern + library for Vue.js applications

It serves as a centralized store for all the components in an application, with rules ensuring that the state can only be mutated in a predictable fashion
- has integration with Vue's official devtools extension

Supports 
- zero-config time-travel debugging 
- state snapshot export / import

---

### Installation

- Direct Download / CDN
```js
https://unpkg.com/vuex
```
Include vuex after Vue and it will install itself automatically

- NPM/YARN
```js
npm install vuex --save
yarn add vuex
```

---
### Module systens
When used with a module system, you must explicitly install Vuex via Vue.use()
```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
```
* You don't need to do this when using global script tags.
* Vuex requires Promise

---
### State Management 
Let's start with a simple Vue counter app:
```js
new Vue({
  // state
  data () { return { count: 0 } },
  // view
  template: ` <div>{{ count }}</div> `,
  // actions
  methods: { increment () { this.count++  } }
})
```

It has
- state, source of truth that drives our app
- view, a declarative mapping of the state
- actions, possible ways the state could change in reaction to user inputs from the view

---
### State Management (2)
Pattern depicted

<img src="/images/flow.png" style="height:50vh" >

---
### The problem
Multiple components that share common state
- multiple views may depend on the same piece of state
- actions from different views may need to mutate the same piece of state

For problem one
- passing props 
    - can be tedious for deeply nested components
    - doesn't work for sibling components

For problem two
- direct parent/child instance references or mutate/synchronize multiple copies of the state via events
    - brittle and lead to unmaintainable code

---
### Solution
Extract the shared state out of the components
- manage it in a global singleton

Component tree becomes a big "view"
- any component can access the state or trigger actions
- no matter where they are in the tree!

Code becomes more structured and maintainable

---
### Solution (2)
<img src="/images/vuex.png">

---
### When Should I Use It?
Costs of Vuex
- more concepts and boilerplate
- trade-off between short term and long term productivity.

Simple apps will be fine without Vuex

Dan Abramov(the author of Redux):
> Flux libraries are like glasses: you’ll know when you need them.

---
### Getting Started
The store
    - a container that holds state
    
Difference between a plain JS Object and a Vuex Store
- Vuex stores are reactive
    - components retrieve state from it
    - they will reactively update if state changes


To change a store's state 
- use mutations
    - this leaves a track-able records which enables tooling 

---
### The Simplest Store
```js
const store = new Vuex.Store({
  state: { count: 0 },
  mutations: { 
    increment (state) {
      state.count++
    }
  }
})
```

- access the state object as store.state
- trigger a state change with the store.commit method

```js
store.commit('increment')
console.log(store.state.count) // -> 1
```

Using store state in a component simply involves returning the state within a computed property, because the store state is reactive

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Hello Vuex



---
### State
Vuex uses a single state tree 
- single object contains all your application level state 
- serves as the "single source of truth"
- you usually will have only one store for each application

You can split your state and mutations into sub modules (later more)

---
### Getting State into Components
Return it from a computed property
```js
// let's create a Counter component
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count
    }
  }
}
```
* whenever store.state.count changes, it will cause the computed property to re-evaluate

---
### Getting State into Components (2)

Previous pattern causes the component to rely on the global store singleton
- Vuex can "inject" the store into all child components from the root

```js
const app = new Vue({
  el: '#app',
  // provide the store using the "store" option.
  // this will inject the store instance to all child components.
  store,
  components: { Counter },
  template: `
    <div class="app"> <counter></counter> </div>
  `
})

const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: { count () { return this.$store.state.count  } }
}
```

---
### MapState 
When component needs multiple store state properties or getters
- mapState helper generates computed getter functions

```js
import { mapState } from 'vuex'
export default {
  computed: mapState({
    count: state => state.count,
    countAlias: 'count',
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}

- or - 

computed: mapState([
  // map this.count to store.state.count
  'count'
])
```

---
### Object Spread Operator
MapState returns an object so how do we use it in combination with other local computed properties? 
```js
computed: {
  localComputed () { /* ... */ },
  // mix this into the outer object with the object spread operator
  ...mapState({ ... })
}
```


---
### Getters, The Problem
Compute derived state based on store state
- example: counting a filtered list

```js
computed: {
  doneTodosCount () {
    return this.$store.state.todos.filter(todo => todo.done).length
  }
}
```

If more than one component needs to make use of this, problem..
Solution:
- duplicate the function
- extract it into a shared helper and import it 
- both are not ideal!


---
### Getters, The Solution
Computed properties for stores
-  getter's result is cached based on its dependencies

```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

Getters can have 
- property style access
- method style access

---
### Property-Style Access
The getters will be exposed on the store.getters object, and you access values as properties:
```js
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

Getters will also receive other getters as the 2nd argument:
```js
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
store.getters.doneTodosCount // -> 1
```

---
### Using getters
We can now easily make use of it inside any component:
```js
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```
* Note that getters accessed as properties are cached 

---

### Method-Style Access
Pass arguments to getters by returning a function
- useful when you want to query an array

```js
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```

* Note that getters accessed via methods is not cached

---
### MapGetters Helper
The mapGetters helper maps store getters to local computed properties
```js
import { mapGetters } from 'vuex'
export default {
  // ...
  computed: {
    ...mapGetters(['doneTodosCount','anotherGetter', ...])
  }
}
```
If you want to map a getter to a different name, use an object:
```js
...mapGetters({
  // map `this.doneCount` to `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

---
### Define mutations
The only way to change state! 

Very similar to events
- each mutation has a string type and a handler

```js
const store = new Vuex.Store({
  state: { count: 1 },
  mutations: {
    increment (state, n) {
        state.count += n
    }
  }
})
```
> "When a mutation with type increment is triggered, call this handler." 

---
### Call a mutation
You call a mutation by commiting an event to the store
```js
store.commit('increment')
store.commit('increment', 10)
```

---
### Object-Style Commit
An alternative way to commit a mutation using an object
```js
store.commit({
  type: 'increment',
  amount: 10
})
```
in the store the payload can be used
```js
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

---
### Mutations Follow Vue's Reactivity Rules
When adding new properties to an Object, you should either
```js
Use Vue.set(obj, 'newProp', 123)
```
or replace object with a fresh one
```js
state.obj = { ...state.obj, newProp: 123 }
```

---
### Using Constants for Mutation Types
It is a commonly seen pattern to use constants for mutation types in various Flux implementations
- allows the code to take advantage of tooling like linters
- keeps code readable and maintainble

```js
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // we can use the ES2015 computed property name feature
    // to use a constant as the function name
    [SOME_MUTATION] (state) { }
  }
})
```

* Whether to use constants is largely a preference 

---
### Committing Mutations in Components
Commit mutations using 
- this.$store.commit('xxx')
- mapMutations helper 

```js
import { mapMutations } from 'vuex'
export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // map `this.increment()` to `this.$store.commit('increment')`
      // `mapMutations` also supports payloads:
      'incrementBy' // map `this.incrementBy(amount)` to `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // map `this.add()` to `this.$store.commit('increment')`
    })
  }
}
```

---
### Actions
Actions are similar to mutations, the differences being that
- Instead of mutating the state, actions commit mutations
- Actions can contain arbitrary asynchronous operations

Let's register a simple action:
```js
const store = new Vuex.Store({
  state: { count: 0 },
  mutations: {
    increment (state) { state.count++ }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

---
### Argument Destructuring
In practice, we often use ES2015 argument destructuring to simplify the code a bit (especially when we need to call commit multiple times):
```js
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

---
### Dispatching Actions
Actions are triggered with the store.dispatch method
```js
store.dispatch('increment')
```
Why don't just call store.commit('increment') directly? 
- mutations have to be synchronous, actions don't
```js
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => { commit('increment') }, 1000)
  }
}
```

---
### Object Style Dispatch
Actions support the same payload format and object-style dispatch
```js
// dispatch with a payload
store.dispatch('incrementAsync', { amount: 10 })
// dispatch with an object
store.dispatch({ type: 'incrementAsync', amount: 10 })
```

---
### A Practical Example
An example would be an action to checkout a shopping cart, which involves calling an async API and committing multiple mutations

```js
actions: {
  checkout ({ commit, state }, products) {
    // save the items currently in the cart
    const savedCartItems = [...state.cart.added]
    // send out checkout request, and optimistically clear the cart
    commit(types.CHECKOUT_REQUEST)
    // the shop API accepts a success callback and a failure callback
    shop.buyProducts(
      products,
      // handle success
      () => commit(types.CHECKOUT_SUCCESS),
      // handle failure
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}
```

* Note performing a flow of asynchronous operations, and recording side effects (state mutations) of the action by committing them

---
### Dispatching Actions in Components
You can dispatch actions in components 
- this.$store.dispatch('xxx')
- mapActions helper which maps component methods to store.dispatch calls

```js
import { mapActions } from 'vuex'
export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // map `this.increment()` to `this.$store.dispatch('increment')`
      // `mapActions` also supports payloads:
      'incrementBy' // map `this.incrementBy(amount)` to `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // map `this.add()` to `this.$store.dispatch('increment')`
    })
  }
}
```

---
### Composing Actions
Actions are often asynchronous
- how do we know when an action is done? 
- how can we compose multiple actions together to handle more complex async flows?

store.dispatch returns a Promise when an action returns a Promise
```js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => { commit('someMutation'); resolve() }, 1000)
    })
  }
}
// in the consumer
store.dispatch('actionA').then(() => {
  // ...
})

// or in another action
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

---
### Async/Await
Actions can be composed like this
```js
actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // wait for `actionA` to finish
    commit('gotOtherData', await getOtherData())
  }
}
```

---
### Modules
All state of our application is contained inside one big object
- store can get really bloated.

Vuex provides modules
- contain its own state, mutations, actions, getters, 
- optionally contains nested modules

```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}
```

---
### Using Modules 
To use modules use
```js
const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> `moduleA`'s state
store.state.b // -> `moduleB`'s state
```

---
### Module Local State
Inside module's mutations and getters, the first argument is the module's local state.

```js
const moduleA = {
  state: { count: 0 },
  mutations: {
    // `state` is the local module state
    increment (state) { state.count++ } }
  ,
  getters: {
    doubleCount (state) { return state.count * 2 }
  }
}
```

---
### Module Local State 
Similarly, context.state will expose the local state, and root state will be exposed as context.rootState
```js
const moduleA = {
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) { commit('increment') }
    }
  }
}
```
Also, inside module getters, the root state will be exposed as their 3rd argument:
```js
const moduleA = {
  getters: {
    sumWithRootCount (state, getters, rootState) { 
       return state.count + rootState.count
    }
  }
}
```

---
### Namespacing
Actions, mutations and getters inside modules are registered globally 
- allows multiple modules to react to the same mutation/action type

Self-containment or reusability can be reused with namespaced: true
Then, when the module is registered, all of its getters, actions and mutations will be automatically namespaced based on the path the module is registered at

For example
```js
const store = new Vuex.Store({
  modules: {
    account: {
      namespaced: true,
      // module state is already nested and not affected by namespace option
      state: { ... },
      getters: { isAdmin () { ... } },// -> getters['account/isAdmin']
      actions: { login ()   { ... } }, // -> dispatch('account/login')
      mutations: { login () { ... } }, // -> commit('account/login')
    }
})
```

---
### Global Assets in Namespaced Modules
If you want to use global state and getters
- rootState and rootGetters as the 3rd and 4th arguments to getter functions
- exposed as properties on the context object passed to action functions

To dispatch actions or commit mutations in the global namespace, pass { root: true } as the 3rd argument to dispatch and commit
```js
modules: {
  foo: {
    namespaced: true,
    getters: {
      // `getters` is localized to this module's getters
      // you can use rootGetters via 4th argument of getters
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },
    actions: {
      // dispatch and commit are also localized for this module
      // they will accept `root` option for the root dispatch/commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'
        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'
        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```

---
### Register Global Action in Namespaced Modules
If you want to register global actions in namespaced modules
- mark it with root: true 
- place the action definition to function handler

For example:
```js
{
  actions: {
    someOtherAction ({dispatch}) { dispatch('someAction') }
  },
  modules: {
    foo: {
      namespaced: true,
      actions: {
        someAction: {
          root: true,
          handler (namespacedContext, payload) { ... } // -> 'someAction'
        }
      }
    }
  }
}
```

---
### Binding Helpers with Namespace
When binding a namespaced module to components with the mapState, mapGetters, mapActions and mapMutations helpers, use

```js
computed: {
  ...mapState({
    a: state => state.some.nested.module.a,
    b: state => state.some.nested.module.b
  })
},
methods: {
  ...mapActions([
    'some/nested/module/foo', // -> this['some/nested/module/foo']()
    'some/nested/module/bar' // -> this['some/nested/module/bar']()
  ])
}
```

But this is super verbose!

---
### Solution
Pass the module namespace string as the first argument to the helpers so that all bindings are done using that module as the context. 

The above can be simplified to:
```js
computed: {
  ...mapState('some/nested/module', { a: state => state.a, b: state => state.b })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo', // -> this.foo()
    'bar' // -> this.bar()
  ])
}
```

---
### createNamespacedHelpers
Create namespaced helpers by using createNamespacedHelpers
- returns an object having new component binding helpers that are bound with the given namespace value

```js
import { createNamespacedHelpers } from 'vuex'
const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')
export default {
  computed: {
    // look up in `some/nested/module`
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // look up in `some/nested/module`
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```

---
### Caveat for Plugin Developers
Your modules will be also namespaced if the plugin users add your modules under a namespaced module

Therefore, you may need to receive a namespace value via your plugin option
```js
// get namespace value via plugin option
// and returns Vuex plugin function
export function createPlugin (options = {}) {
  return function (store) {
    // add namespace to plugin module's types
    const namespace = options.namespace || ''
    store.dispatch(namespace + 'pluginAction')
  }
}
```

---
### Dynamic Module Registration
Dynamic module registration makes it possible for other Vue plugins to use Vuex for state management by attaching a module to the application's store
- you can remove a dynamically registered module with store.unregisterModule(moduleName)

* Note you cannot remove static modules (declared at store creation) with this method.


---
### Dynamic Module Registration (2)
You can register a module after the store has been created with the store.registerModule method

```js
// register a module `myModule`
store.registerModule('myModule', { ... })
// register a nested module `nested/myModule`
store.registerModule(['nested', 'myModule'], { ... })
```

The module's state will be exposed as 
- store.state.myModule
- store.state.nested.myModule

---
### Preserving state
Preserve previous state when registering a new module
- state from a Server Side Rendered app

```js
store.registerModule('a', module, { preserveState: true })
```

When you set preserveState: true
- the module is registered, actions, mutations and getters are added to the store
- the state not added to the store
    - assumes that your store state already contains state, no overwrite

---
### Module Reuse
Sometimes we may need to create multiple instances of a module, for example:
- creating multiple stores that use the same module 
- register the same module multiple times in the same store

If we use a plain object to declare the state of the module
- that state object will be shared by reference
- causes cross store/module state pollution when it's mutated

Remember: this is actually the exact same problem with data inside Vue components

---
### Module Reuse (2)
Solution 
- use a function for declaring module state

```js
const MyReusableModule = {
  state () {
    return { foo: 'bar' }
  },
  // mutations, actions, getters...
}
```

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Creating a VUEX Store

---

<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Build a VUEX Store
