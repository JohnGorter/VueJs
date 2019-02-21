# More Vuex

---
### Application Structure
Rules
- application-level state is centralized in the store.
- the only way to mutate the state is by committing mutations, which are synchronous transactions
- asynchronous logic should be encapsulated in, and can be composed with actions.

---
### Example project structure
For any non-trivial app, we will likely need to leverage modules. Here's an example project structure:

```js
├── index.html
├── main.js
├── api
│   └── ... # abstractions for making API requests
├── components
│   ├── App.vue
│   └── ...
└── store
    ├── index.js          # where we assemble modules and export the store
    ├── actions.js        # root actions
    ├── mutations.js      # root mutations
    └── modules
        ├── cart.js       # cart module
        └── products.js   # products module
```

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Application Structure
https://github.com/vuejs/vuex/tree/dev/examples/shopping-cart

---
### Plugins
Vuex stores accept the plugins option that exposes hooks for each mutation
- a Vuex plugin is a function that receives the store as the only argument

```js
const myPlugin = store => {
  // called when the store is initialized
  store.subscribe((mutation, state) => {
    // called after every mutation. The mutation comes in the format of `{ type, payload }
  })
}
```
And can be used like this:
```js
const store = new Vuex.Store({
  // ...
  plugins: [myPlugin]
})
```

---
### Committing Mutations Inside Plugins
Plugins are not allowed to directly mutate state 
- similar to components, they can only commit mutations
    
By committing mutations
- a plugin can be used to sync a data source to the store
- example, sync a websocket data source to the store

```js
export default function createWebSocketPlugin (socket) {
  return store => {
    socket.on('data', data => { store.commit('receiveData', data) })
    store.subscribe(mutation => {
      if (mutation.type === 'UPDATE_DATA') { socket.emit('update', mutation.payload) }
    })
  }
}
const plugin = createWebSocketPlugin(socket)
const store = new Vuex.Store({
  state,
  mutations,
  plugins: [plugin]
})
```

---
### Taking State Snapshots
A plugin may want to receive "snapshots" of the state
- compare the post-mutation state with pre-mutation state

To achieve that, you will need to perform a deep-copy on the state object:
```js
const myPluginWithSnapshot = store => {
  let prevState = _.cloneDeep(store.state)
  store.subscribe((mutation, state) => {
    let nextState = _.cloneDeep(state)
    // compare `prevState` and `nextState`...
    // save state for next mutation
    prevState = nextState
  })
}
```

---
### Taking State Snapshots
Plugins that take state snapshots should be used only during development. 
When using webpack or Browserify, we can let our build tools handle that:
```js
const store = new Vuex.Store({
  plugins: process.env.NODE_ENV !== 'production' ? [myPluginWithSnapshot] : []
})
```

The plugin will be used by default. For production, you will need DefinePlugin for webpack or envify for Browserify to convert the value of process.env.NODE_ENV !== 'production' to false for the final build.

---
### Built-in Logger Plugin
Vuex comes with a logger plugin for common debugging usage:
```js
import createLogger from 'vuex/dist/logger'
const store = new Vuex.Store({
  plugins: [logger]
})

const logger = createLogger({
  collapsed: false, // auto-expand logged mutations
  filter (mutation, stateBefore, stateAfter) {
    // returns `true` if a mutation should be logged
    // `mutation` is a `{ type, payload }`
    return mutation.type !== "aBlacklistedMutation"
  },
  transformer (state) {
    // transform the state before logging it.
    // for example return only a specific sub-tree
    return state.subTree
  },
  mutationTransformer (mutation) {
    // mutations are logged in the format of `{ type, payload }`
    // we can format it any way we want.
    return mutation.type
  },
  logger: console, // implementation of the `console` API, default `console`
})
```

* Note the logger plugin takes state snapshots, so use it only during development.

---
### Strict Mode
To enable strict mode, simply pass in strict: true when creating a Vuex store:
```js
const store = new Vuex.Store({
  // ...
  strict: true
})
```
In strict mode
- whenever Vuex state is mutated outside of mutation handlers => error thrown

---
### Development vs. Production
Do not enable strict mode when deploying for production! 
- Strict mode runs a synchronous deep watcher on the state tree for detecting inappropriate mutations
- it can be quite expensive when you make large amount of mutations

Make sure to turn it off in production to avoid the performance cost.
Similar to plugins, we can let the build tools handle that:
```js
const store = new Vuex.Store({
  // ...
  strict: process.env.NODE_ENV !== 'production'
})
```

---
### Form Handling Challenge
When using Vuex in strict mode, it could be a bit tricky to use v-model on a piece of state that belongs to Vuex

```js
<input v-model="obj.message">
```

v-model here will attempt to directly mutate obj.message when the user types in the input
In strict mode => an error because the mutation is not performed inside mutation handler!


---
### Form Handling Challenge (2) 
Solution
- binding the input's value and call an action on the input or change event
```js
<input :value="message" @input="updateMessage">

// ...
computed: {
  ...mapState({ message: state => state.obj.message })
},
methods: {
  updateMessage (e) { this.$store.commit('updateMessage', e.target.value) }
}
```
And here's the mutation handler:
```js
// ...
mutations: {
  updateMessage (state, message) { state.obj.message = message }
}
```

---
### Two-way Computed Property
This is quite a bit more verbose than v-model + local state

An alternative approach is using a two-way computed property with a setter
```js
<input v-model="message">
// ...
computed: {
  message: {
    get () { return this.$store.state.obj.message },
    set (value) { this.$store.commit('updateMessage', value) }
  }
}
```

---
### Testing
The main parts to unit test in Vuex are mutations and actions

Testing Mutations
- mutations are just functions that completely rely on their arguments
- export the mutations as a named export
```js
const state = { ... }
// export `mutations` as a named export
export const mutations = { ... }
export default new Vuex.Store({
  state,
  mutations
})
```
---
### Testing (2)
Example testing a mutation using Mocha + Chai
```js
// mutations.js
export const mutations = {
  increment: state => state.count++
}
// mutations.spec.js
import { expect } from 'chai'
import { mutations } from './store'

// destructure assign `mutations`
const { increment } = mutations

describe('mutations', () => {
  it('INCREMENT', () => {
    // mock state
    const state = { count: 0 }
    // apply mutation
    increment(state)
    // assert result
    expect(state.count).to.equal(1)
  })
})
```

---
### Testing Actions
Actions are a bit more tricky because they may call out to external APIs
- usually need to do some level of mocking 
- for example, abstract the API calls into a service and mock that service 

In order to easily mock dependencies, use webpack and inject-loader to bundle our test files

---
### Testing Actions (2)
Example testing an async action:
```js
// actions.js
import shop from '../api/shop'

export const getAllProducts = ({ commit }) => {
  commit('REQUEST_PRODUCTS')
  shop.getProducts(products => {
    commit('RECEIVE_PRODUCTS', products)
  })
}
// actions.spec.js

// use require syntax for inline loaders.
// with inject-loader, this returns a module factory
// that allows us to inject mocked dependencies.
import { expect } from 'chai'
const actionsInjector = require('inject-loader!./actions')

// create the module with our mocks
const actions = actionsInjector({
  '../api/shop': {
    getProducts (cb) {
      setTimeout(() => {
        cb([ /* mocked response */ ])
      }, 100)
    }
  }
})

// helper for testing action with expected mutations
const testAction = (action, payload, state, expectedMutations, done) => {
  let count = 0

  // mock commit
  const commit = (type, payload) => {
    const mutation = expectedMutations[count]

    try {
      expect(type).to.equal(mutation.type)
      if (payload) {
        expect(payload).to.deep.equal(mutation.payload)
      }
    } catch (error) {
      done(error)
    }

    count++
    if (count >= expectedMutations.length) {
      done()
    }
  }

  // call the action with mocked store and arguments
  action({ commit, state }, payload)

  // check if no mutations should have been dispatched
  if (expectedMutations.length === 0) {
    expect(count).to.equal(0)
    done()
  }
}

describe('actions', () => {
  it('getAllProducts', done => {
    testAction(actions.getAllProducts, null, {}, [
      { type: 'REQUEST_PRODUCTS' },
      { type: 'RECEIVE_PRODUCTS', payload: { /* mocked response */ } }
    ], done)
  })
})
```

---
### Spies
If you have spies available in your testing environment, use them instead of the testAction helper:
```js
describe('actions', () => {
  it('getAllProducts', () => {
    const commit = sinon.spy()
    const state = {}
    
    actions.getAllProducts({ commit, state })
    
    expect(commit.args).to.deep.equal([
      ['REQUEST_PRODUCTS'],
      ['RECEIVE_PRODUCTS', { /* mocked response */ }]
    ])
  })
})
```

---
### Testing Getters
If your getters have complicated computation, test them
- also very straightforward to test

Example testing a getter:
```js
// getters.js
export const getters = {
  filteredProducts (state, { filterCategory }) {
    return state.products.filter(product => {
      return product.category === filterCategory
    })
  }
}
// getters.spec.js
import { expect } from 'chai'
import { getters } from './getters'

describe('getters', () => {
  it('filteredProducts', () => {
    // mock state
    const state = {
      products: [
        { id: 1, title: 'Apple', category: 'fruit' },
        { id: 2, title: 'Orange', category: 'fruit' },
        { id: 3, title: 'Carrot', category: 'vegetable' }
      ]
    }
    // mock getter
    const filterCategory = 'fruit'

    // get the result from the getter
    const result = getters.filteredProducts(state, { filterCategory })

    // assert the result
    expect(result).to.deep.equal([
      { id: 1, title: 'Apple', category: 'fruit' },
      { id: 2, title: 'Orange', category: 'fruit' }
    ])
  })
})
```

---
### Running Tests
Properly written tests for mutations and actions 
- have no direct dependency on Browser APIs after proper mocking. 
- can be run it directly in NodeJS

Running in Node
Create the following webpack config (together with proper .babelrc):
```js
// webpack.config.js
module.exports = {
  entry: './test.js',
  output: {
    path: __dirname,
    filename: 'test-bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        loader: 'babel-loader',
        exclude: /node_modules/
      }
    ]
  }
}
```
Then
```js
webpack
mocha test-bundle.js
```

---
### Running in Browser

- install mocha-loader
- Change the entry from the webpack config above to 'mocha-loader!babel-loader!./test.js'
- Start webpack-dev-server using the config
- Go to localhost:8080/webpack-dev-server/test-bundle

---
### Hot Reloading
Vuex supports hot-reloading mutations, modules, actions and getters during development using 
- webpack's Hot Module Replacement API
- Browserify with the browserify-hmr plugin

For mutations and modules, you need to use the store.hotUpdate() API method:
```js
// store.js
import Vue from 'vue'
import Vuex from 'vuex'
import mutations from './mutations'
import moduleA from './modules/a'

Vue.use(Vuex)

const state = { ... }

const store = new Vuex.Store({
  state,
  mutations,
  modules: {
    a: moduleA
  }
})

if (module.hot) {
  // accept actions and mutations as hot modules
  module.hot.accept(['./mutations', './modules/a'], () => {
    // require the updated modules
    // have to add .default here due to babel 6 module output
    const newMutations = require('./mutations').default
    const newModuleA = require('./modules/a').default
    // swap in the new modules and mutations
    store.hotUpdate({
      mutations: newMutations,
      modules: {
        a: newModuleA
      }
    })
  })
}
```

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Creating a VUEX Plugin

---

<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Build a VUEX Plugin

