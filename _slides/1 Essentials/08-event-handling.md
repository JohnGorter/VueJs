# Event Handling

---
### V-On or @
V-on is to listen to DOM events and run JavaScript 
- all handler functions are bound to the ViewModel that’s handling the current view
- easy to locate handler function implementation within JS code by skimming the template
- No manually attach event listeners in JS, ViewModel code can be pure logic and DOM-free
  - easier to test
- when ViewModel is destroyed, all event listeners are automatically removed

```js
<div id="example-1">
  <button v-on:click="counter += 1">Add 1</button>
  <p>The button above has been clicked {{ counter }} times.</p>
</div>
var example1 = new Vue({
  el: '#example-1',
  data: { counter: 0 }
})
```

---
### Method event handlers
Use a method to handle the event using different approaches
```js
<div id="example-2">
  <!-- `greet` is the name of a method defined below -->
  <button v-on:click="greet">Greet</button>
  <!-- `say` is inline javascript expression -->
  <button v-on:click="say('hi')">Say hi</button>
  <button v-on:click="say('what')">Say what</button>
</div>

new Vue({
  el: '#example-3',
  data: {name:'john'},
  methods: {
    greet: function (message) { alert('hello' + this.name)
    say: function (message) { alert(message) }
  }
})
```
* Notice the two ways of binding here

---
### Event variable
Sometimes you have to have access to the original event
```js
<button v-on:click="warn('Form cannot be submitted yet.', $event)">Submit</button>
// ...
methods: {
  warn: function (message, event) {
    if (event) event.preventDefault()
    alert(message)
  }
}
```

---
### Event Modifiers
Shorter event.preventDefault() or event.stopPropagation() inside event handlers
- event modifiers for v-on
- .stop, .prevent, .capture, .self, .once, .passive
```js
<!-- the click events propagation will be stopped -->
<a v-on:click.stop="doThis"></a>
<!-- the submit event will no longer reload the page -->
<form v-on:submit.prevent="onSubmit"></form>
<!-- modifiers can be chained -->
<a v-on:click.stop.prevent="doThat"></a>
<!-- just the modifier -->
<form v-on:submit.prevent></form>
<!-- use capture mode when adding the event listener -->
<!-- i.e. an event targeting an inner element is handled here before being handled by that element -->
<div v-on:click.capture="doThis">...</div>
<!-- only trigger handler if event.target is the element itself -->
<!-- i.e. not from a child element -->
<div v-on:click.self="doThat">...</div>
<!-- the click event will be triggered at most once -->
<a v-on:click.once="doThis"></a>
```
* when chaining, order matters!

---
### Key Modifiers
Listening for specific keys
  - add key modifiers 
```js
<!-- only call `vm.submit()` when the `key` is `Enter` -->
<input v-on:keyup.enter="submit">
<!-- the handler will only be called if $event.key is equal to 'PageDown' -->
<input v-on:keyup.page-down="onPageDown">
<input v-on:keyup.13="submit">
```

and even more 
- .enter, .tab, .delete (captures both “Delete” and “Backspace” keys), .esc, .space, .up, .down, .left, .right

* custom key modifier aliases can be added via global config.keyCodes
```js
// enable `v-on:keyup.f1`
Vue.config.keyCodes.f1 = 112
```

---
### System Modifier Keys
Use following modifiers to trigger event listeners only when modifier key is pressed
- .ctrl, .alt, .shift, .meta
- On Macintosh keyboards, meta is the command key (⌘)
- On Windows keyboards, meta is the windows key (⊞)
- On Sun Microsystems keyboards, meta is marked as a solid diamond (◆)

```js
<!-- Alt + C -->
<input @keyup.alt.67="clear">
<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```
- keyup.ctrl will only trigger if you release a key while holding down ctr
- It won’t trigger if you just release the ctrl key
- For that behavior, use the keyCode for ctrl -> keyup.17

---
### Exact Modifier
This modifier allows control of the exact combination of system modifiers needed to trigger an event.
```js
<!-- this will fire even if Alt or Shift is also pressed -->
<button @click.ctrl="onClick">A</button>
<!-- this will only fire when Ctrl and no other keys are pressed -->
<button @click.ctrl.exact="onCtrlClick">A</button>
<!-- this will only fire when no system modifiers are pressed -->
<button @click.exact="onClick">A</button>
```

---
### Mouse Button Modifiers
These modifiers restrict the handler to events triggered by a specific mouse button
- .left, .right, .middle


---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Conditionally rendering elements




