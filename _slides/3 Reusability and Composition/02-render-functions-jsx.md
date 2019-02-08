# Render functions and JSX

---
### Basics
Vue recommends using templates to build your HTML in the vast majority of cases
- there are situations, where the full programmatic power of JavaScript is needed
- that’s where render functions come in
- a closer-to-the-compiler alternative to templates

Suppose you want to generate anchored headings
```js
<h1>
  <a name="hello-world" href="#hello-world">  Hello world! </a>
</h1>
```
For the HTML above, you decide you want this component interface:
```js
<anchored-heading :level="1">Hello world!</anchored-heading>
```

---
### Basics (2)
A component that only generates a heading based on the level prop
looks like
```js
<script type="text/x-template" id="anchored-heading-template">
  <h1 v-if="level === 1"><slot></slot></h1>
  <h2 v-else-if="level === 2"><slot></slot></h2>
  <h3 v-else-if="level === 3"><slot></slot></h3>
</script>
Vue.component('anchored-heading', {
  template: '#anchored-heading-template',
  props: { level: {  type: Number, required: true } }
})
```

That template doesn’t feel great. It’s not only verbose, but we’re duplicating <slot></slot> for every heading level and will have to do the same when we add the anchor element.

---
### Basics (3)
- let’s try rewriting it with a render function
```js
Vue.component('anchored-heading', {
  render: function (createElement) {
    return createElement(
      'h' + this.level,   // tag name
      this.$slots.default // array of children
    )
  },
  props: { level: {  type: Number,  required: true } }
})
```

Much simpler! 


---
### Nodes, Trees, and the Virtual DOM
When a browser reads this code, it builds a tree of “DOM nodes” to help it keep track of everything
```js
<div>
  <h1>My title</h1>
  Some text content
  <!-- TODO: Add tagline  -->
</div>
```

- Every element is a node
- Every piece of text is a node
- Even comments are nodes! 

A node is just a piece of the page

---
### Nodes, Trees, and the Virtual DOM (2)
you tell Vue what HTML you want on the page, in a template
```js
<h1>{{ blogTitle }}</h1>
```
Or a render function:
```js
render: function (createElement) {
  return createElement('h1', this.blogTitle)
}
```

Vue automatically keeps the page updated, even when blogTitle changes.

---
### The Virtual DOM
Virtual DOM keeps track of the changes it needs to make to the real DOM. Taking a closer look at this line:
```js
return createElement('h1', this.blogTitle) // returns a createNodeDescription
```
What is createElement actually returning? It’s not exactly a real DOM element. 

---
### createElement Arguments
how to use template features in the createElement function 
- using arguments 

arguments that createElement accepts
```js
// @returns {VNode}
createElement(
  // {String | Object | Function} An HTML tag name, component options, or async function resolving to one of these. Required.
  'div',
  // {Object} A data object corresponding to the attributes you would use in a template. Optional.
  {
    // (see details in the next section below)
  },
  // {String | Array} Children VNodes, built using `createElement()` or using strings to get 'text VNodes'. Optional.
  [
    'Some text comes first.',
    createElement('h1', 'A headline'),
    createElement(MyComponent, { props: { someProp: 'foobar' } })
  ]
)
```

---
### The Data Object In-Depth
The data object has the following options
```js
{
  // Same API as `v-bind:class`, accepting either a string, object, or array of strings and objects.
  class: { foo: true, bar: false },
  // Same API as `v-bind:style`, accepting either a string, object, or array of objects.
  style: { color: 'red', fontSize: '14px' },
  // Normal HTML attributes
  attrs: { id: 'foo' },
  // Component props
  props: { myProp: 'bar' },
  // DOM properties
  domProps: { innerHTML: 'baz' },
  // Event handlers are nested under `on`, though modifiers such as in `v-on:keyup.enter` are not
  // supported. You'll have to manually check the keyCode in the handler instead.
  on: { click: this.clickHandler },
  // For components only. Allows you to listen to native events, rather than events emitted from
  // the component using `vm.$emit`.
  nativeOn: { click: this.nativeClickHandler },
  // Custom directives. Note that the `binding`'s`oldValue` cannot be set, as Vue keeps track
  // of it for you.
  directives: [
    { name: 'my-custom-directive', value: '2', expression: '1 + 1', arg: 'foo', modifiers: { bar: true } }
  ],
  // Scoped slots in the form of { name: props => VNode | Array<VNode> }
  scopedSlots: { default: props => createElement('span', props.text) },
  // The name of the slot, if this component is the child of another component
  slot: 'name-of-slot',
  // Other special top-level properties
  key: 'myKey',
  ref: 'myRef',
  // If you are applying the same ref name to multiple elements in the render function. This will make `$refs.myRef` become an
  // array
  refInFor: true
}
```

---
### Example 
With this knowledge, we can now finish the component we started
```js
var getChildrenTextContent = function (children) {
  return children.map(function (node) {
    return node.children ? getChildrenTextContent(node.children) : node.text
  }).join('')
}

Vue.component('anchored-heading', {
  render: function (createElement) {
    // create kebab-case id
    var headingId = getChildrenTextContent(this.$slots.default)
      .toLowerCase().replace(/\W+/g, '-').replace(/(^-|-$)/g, '')

    return createElement(
      'h' + this.level,
      [ createElement('a', { attrs: { name: headingId, href: '#' + headingId } }, this.$slots.default)]
    )
  },
  props: {
    level: { type: Number, required: true }
  }
})
```

---
### Constraints
- VNodes must be unique
- this means the following render function is invalid:
```js
render: function (createElement) {
  var myParagraphVNode = createElement('p', 'hi')
  return createElement('div', [
    // Yikes - duplicate VNodes!
    myParagraphVNode, myParagraphVNode
  ])
}
```
If you really want to duplicate the same element/component many times, use a factory function
- the following render function is a perfectly valid way of rendering 20 identical paragraphs
```js
render: function (createElement) {
  return createElement('div',
    Array.apply(null, { length: 20 }).map(function () {
      return createElement('p', 'hi')
    })
  )
}
```

---
### Template feature replacements
- use plain JavaScript
  - wherever something can be easily accomplished in plain JS, there is no proprietary alternative
  - for example, in a template using v-if and v-for:
```js
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```
could be rewritten with JavaScript’s if/else and map in a render function:
```js
props: ['items'],
render: function (createElement) {
  if (this.items.length) {
    return createElement('ul', this.items.map(function (item) {
      return createElement('li', item.name)
    }))
  } else {
    return createElement('p', 'No items found.')
  }
}
```

---
### V-Model
There is no direct v-model counterpart in render functions 
- you will have to implement the logic yourself
```js
props: ['value'],
render: function (createElement) {
  var self = this
  return createElement('input', {
    domProps: {  value: self.value },
    on: {
      input: function (event) {
        self.$emit('input', event.target.value)
      }
    }
  })
}
```

---
### Event & Key Modifiers
For the .passive, .capture and .once event modifiers, Vue offers prefixes that can be used with on

|Modifier(s)	 | Prefix |
|----|----|
|.passive	| & |
|.capture	| ! |
|.once	| [tilde] |
|.capture.once | or |
|.once.capture	| [tilde]! |

example

```js
on: {
  '!click': this.doThisInCapturingMode,
  '[tilde]keyup': this.doThisOnce,
  '[tilde]!mouseover': this.doThisOnceInCapturingMode
}
```


---
### Event & Key Modifiers
For all other event and key modifiers, no proprietary prefix is necessary, because you can use event methods in the handler:

|Modifier(s) |	Equivalent in Handler |
|----|----|
|.stop	| event.stopPropagation() |
|.prevent	| event.preventDefault() |
|.self |	if (event.target !== event.currentTarget) return |
|Keys: .enter, .13 |	if (event.keyCode !== 13) return (change 13 to another key code for other key modifiers) |
|Modifiers Keys: .ctrl, .alt, .shift, .meta |	if (!event.ctrlKey) return (change ctrlKey to altKey, shiftKey, or metaKey, respectively) |

---
### Example
Here’s an example with all of these modifiers used together:
```js
on: {
  keyup: function (event) {
    if (event.target !== event.currentTarget) return
    if (!event.shiftKey || event.keyCode !== 13) return
    event.stopPropagation()
    event.preventDefault()
  }
}
```

---
### Slots
You can access static slot contents as Arrays of VNodes from this.$slots:
```js
render: function (createElement) {
  // `<div><slot></slot></div>`
  return createElement('div', this.$slots.default)
}
```

And access scoped slots as functions that return VNodes from this.$scopedSlots:
```js
props: ['message'],
render: function (createElement) {
  // `<div><slot :text="message"></slot></div>`
  return createElement('div', [
    this.$scopedSlots.default({
      text: this.message
    })
  ])
}
```

---
### Slots (2)
To pass scoped slots to a child component using render functions, use the scopedSlots field in VNode data:
```js
render: function (createElement) {
  return createElement('div', [
    createElement('child', {
      // pass `scopedSlots` in the data object
      // in the form of { name: props => VNode | Array<VNode> }
      scopedSlots: {
        default: function (props) {
          return createElement('span', props.text)
        }
      }
    })
  ])
}
```

---
### JSX
If you’re writing a lot of render functions, it might feel painful to write something like this:
```js
createElement(
  'anchored-heading', {
    props: { level: 1 }
  }, [
    createElement('span', 'Hello'),
    ' world!'
  ]
)
```
Especially when the template version is so simple in comparison:
```
<anchored-heading :level="1">
  <span>Hello</span> world!
</anchored-heading>
```

---
### JSX (2)
That’s why there’s a Babel plugin to use JSX with Vue
```js
import AnchoredHeading from './AnchoredHeading.vue'
new Vue({
  el: '#demo',
  render: function (h) {
    return (
      <AnchoredHeading level={1}>
        <span>Hello</span> world!
      </AnchoredHeading>
    )
  }
})
```

Aliasing createElement to h is a common convention you’ll see in the Vue ecosystem
- actually required for JSX
- we automatically inject const h = this.$createElement in any method and getter 
declared in ES2015 syntax that has JSX,


---
### Functional Components
for relatively simple components, we can mark components as functional, 
- they’re stateless (no reactive data) 
- instanceless (no this context)

```js
Vue.component('my-component', {
  functional: true, // Props are optional
  props: { },
  // To compensate for the lack of an instance,
  // we are now provided a 2nd context argument.
  render: function (createElement, context) { }
})
```

Note: in versions before 2.3.0, the props option is required if you wish to accept props in a functional component. In 2.3.0+ you can omit the props option and all attributes found on the component node will be implicitly extracted as props.

In 2.5.0+, if you are using single-file components, template-based functional components can be declared with:

<template functional>
</template>
Everything the component needs is passed through context, which is an object containing:

props: An object of the provided props
children: An array of the VNode children
slots: A function returning a slots object
scopedSlots: (2.6.0+) An object that exposes passed-in scoped slots. Also exposes normal slots as functions.
data: The entire data object, passed to the component as the 2nd argument of createElement
parent: A reference to the parent component
listeners: (2.3.0+) An object containing parent-registered event listeners. This is an alias to data.on
injections: (2.3.0+) if using the inject option, this will contain resolved injections.
After adding functional: true, updating the render function of our anchored heading component would require adding the context argument, updating this.$slots.default to context.children, then updating this.level to context.props.level.

Since functional components are just functions, they’re much cheaper to render. However, the lack of a persistent instance means they won’t show up in the Vue devtools component tree.

They’re also very useful as wrapper components. For example, when you need to:

Programmatically choose one of several other components to delegate to
Manipulate children, props, or data before passing them on to a child component
Here’s an example of a smart-list component that delegates to more specific components, depending on the props passed to it:

var EmptyList = { /* ... */ }
var TableList = { /* ... */ }
var OrderedList = { /* ... */ }
var UnorderedList = { /* ... */ }

Vue.component('smart-list', {
  functional: true,
  props: {
    items: {
      type: Array,
      required: true
    },
    isOrdered: Boolean
  },
  render: function (createElement, context) {
    function appropriateListComponent () {
      var items = context.props.items

      if (items.length === 0)           return EmptyList
      if (typeof items[0] === 'object') return TableList
      if (context.props.isOrdered)      return OrderedList

      return UnorderedList
    }

    return createElement(
      appropriateListComponent(),
      context.data,
      context.children
    )
  }
})

---
### Passing Attributes and Events to Child Elements/Components
On normal components, attributes not defined as props are automatically added to the root element of the component, replacing or intelligently merging with any existing attributes of the same name.
- Functional components, however, require you to explicitly define this behavior:
```js
Vue.component('my-functional-button', {
  functional: true,
  render: function (createElement, context) {
    // Transparently pass any attributes, event listeners, children, etc.
    return createElement('button', context.data, context.children)
  }
})
```
By passing context.data as the second argument to createElement, we are passing down any attributes or event listeners used on my-functional-button. It’s so transparent, in fact, that events don’t even require the .native modifier.

If you are using template-based functional components, you will also have to manually add attributes and listeners. Since we have access to the individual context contents, we can use data.attrs to pass along any HTML attributes and listeners (the alias for data.on) to pass along any event listeners.

<template functional>
  <button
    class="btn btn-primary"
    v-bind="data.attrs"
    v-on="listeners"
  >
    <slot/>
  </button>
</template>
slots() vs children
You may wonder why we need both slots() and children. Wouldn’t slots().default be the same as children? In some cases, yes - but what if you have a functional component with the following children?

<my-functional-component>
  <p slot="foo">
    first
  </p>
  <p>second</p>
</my-functional-component>
For this component, children will give you both paragraphs, slots().default will give you only the second, and slots().foo will give you only the first. Having both children and slots() therefore allows you to choose whether this component knows about a slot system or perhaps delegates that responsibility to another component by passing along children.

Template Compilation
You may be interested to know that Vue’s templates actually compile to render functions. This is an implementation detail you usually don’t need to know about, but if you’d like to see how specific template features are compiled, you may find it interesting. Below is a little demo using Vue.compile to live-compile a template string:


render:
function anonymous(
) {
  with(this){return _c('div',[_m(0),(message)?_c('p',[_v(_s(message))]):_c('p',[_v("No message.")])])}
}
staticRenderFns:
_m(0): function anonymous(
) {
  with(this){return _c('header',[_c('h1',[_v("I'm a template!")])])}
}
---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Custom and native events








