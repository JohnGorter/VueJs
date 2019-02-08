# Filters

---
### Filter
- define filters that can be used to apply common text formatting
- filters are usable in two places
  - mustache interpolations 
  - v-bind expressions (the latter supported in 2.1.0+)
- filters should be appended to the end of the JavaScript expression, denoted by the “pipe” symbol

```js
<!-- in mustaches -->
{{ message | capitalize }}

<!-- in v-bind -->
<div v-bind:id="rawId | formatId"></div>
```
---
### Local filters
You can define local filters in a component’s options:
```js
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```
or define a filter globally before creating the Vue instance:
```js
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({ })
```


---
### Filter chaining
Filters can be chained
```js
{{ message | filterA | filterB }}
```

In this case, filterA, defined with a single argument

---
### Filter arguments
Filters are JavaScript functions, therefore they can take arguments
```js
{{ message | filterA('arg1', arg2) }}
```

Here filterA is defined as a function taking three arguments


---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Building a filter








