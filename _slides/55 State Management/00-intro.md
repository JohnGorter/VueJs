# State Management


---
### State Management
Were do we keep state
- Components have state
- Component can share state
  - Parent - Child
  - Siblings
  - Complex trees

---
### Pass state 
- To pass state bewtween parent and child
  - use props
- To pass state between siblings
  - use event bus
- To pass state in complex trees
  - use Vuex

---
### Vue Event Bus
Siblings can pass state using Vue Event Bus using
- $emit, $on api
- event bus / publish-subscribe pattern

---
### Using the Event Bus
Sending events using event bus
```js
<template>
  <div class="pleeease-click-me" @click="emitGlobalClickEvent()"></div>
</template>
<script>
const EventBus = new Vue();
export default {
  data() {  return {  clickCount: 0  } },

  methods: {
    emitGlobalClickEvent() {
      this.clickCount++;
      // Send the event on a channel (i-got-clicked) with a payload (the click count.)
      EventBus.$emit('i-got-clicked', this.clickCount);
    }
  }
}
</script>
```
---
### Using the Event Bus
Receiving Events using event bus

```js
// Listen for the i-got-clicked event and its payload.
EventBus.$on('i-got-clicked', clickCount => {
  console.log(`Oh, that's nice. It's gotten ${clickCount} clicks! :)`)
});
```
If you’d only like to listen for the first emission of an event
- use EventBus.$once(channel: string, callback(payload1,…))

---
### Removing Event Listeners
You can unregister from a channel
```js
// Import the EventBus we just created.
import { EventBus } from './event-bus.js';
// The event handler function.
const clickHandler = function(clickCount) {
  console.log(`Oh, that's nice. It's gotten ${clickCount} clicks! :)`)
}
// Listen to the event.
EventBus.$on('i-got-clicked', clickHandler);
// Stop listening.
EventBus.$off('i-got-clicked', clickHandler);
```
- remove all listeners for a particular event using EventBus.$off(‘i-got-clicked’) with no callback argument
- remove every single listener from EventBus, regardless of channel, call EventBus.$off() with no arguments 

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Vue event bus


<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Implement an event bus
