# Template Syntax

---
### Vue Instance
Template is HTML-based
- all VueJS templates are valid HTML
- templates are compiled to Virtual DOM render functions


If you prefer ReactJS style, you can write render function too
- you even can use JSX syntax

---
### Interpolations

“Mustache” syntax 
```js
<span>Message: {{ msg }}</span>
```
one-time interpolations
```js
<span v-once>This will never change: {{ msg }}</span>
```
output HTML
```js
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

---
#Attributes
Mustaches cannot be used inside HTML attributes
Instead, use a v-bind directive
```js
<div v-bind:id="dynamicId"></div>
```
In the case of boolean attributes,v-bind works differently
```js
<button v-bind:disabled="isButtonDisabled">Button</button>
```

When isButtonDisabled is null/undefined/false, the attribute won't be rendered

---
### JavaScript Expressions
JavaScript expressions can be used inside data bindings:
```js
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```
Each binding can only contain one single expression
So the following will NOT work
```js
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```
Do you see why?

---
### White boxes globals
Template expressions are sandboxed
- ony access to whitelist of globals

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Whitelist Globals

---
### Directives
- special attributes with the v- prefix
- a single JavaScript expression
- it's job is to reactively apply side effects to the DOM when the value of its expression changes

```js
<p v-if="seen">Now you see me</p>
```
The v-if directive would remove/insert the <p> element based on the value

---
### Arguments
Some directives can take an “argument”
```js
<a v-bind:href="url"> ... </a>
<a :href="url"> ... </a>
```
Here href is the argument

Another example is the v-on directive, which listens to DOM events:
```js
<a v-on:click="doSomething"> ... </a>
<a @click="doSomething"> ... </a>
```
Here the argument is the event name to listen to.

---
### Dynamic arguments
It is possible to use a JavaScript expression in a directive argument
```js
<a v-bind:[attributeName]="url"> ... </a>
```
- attributeName will be dynamically evaluated as a JavaScript expression
- its evaluated value will be used as the final value for the argument
- so if the instance has attributeName whose value is "href", then this binding will be like v-bind:href

---
### Dynamic Arguments(2)
Dynamic arguments can be used to bind a handler to a dynamic event name:
```js
<a v-on:[eventName]="doSomething"> ... </a>
```
Similarly, when eventName‘s value is "focus", for example, v-on:[eventName] will be equivalent to v-on:focus.

---
### Constraints
Dynamic Argument Value Constraints
- evaluate to a string, with the exception of null
- anything else triggers a warning in the console

Dynamic Argument Expression Constraints
- spaces and quotes are invalid inside HTML attribute names

---
### Example Constraints
For example, the following is invalid:
```js
<!-- This will trigger a compiler warning. -->
<a v-bind:['foo' + bar]="value"> ... </a>
```
- use expressions without spaces or quotes
- replace the complex expression with a computed property

---
### Modifiers
Special postfixes denoted by a dot, indicate that a directive should be bound in some special way. 

For example
```js
<form v-on:submit.prevent="onSubmit"> ... </form>
```

---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Template Syntax




