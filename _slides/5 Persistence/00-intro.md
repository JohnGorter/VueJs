# Persistence


---
### Persistence 
Persistence can be stored
- client side
    - local storage
- server side
    - services


---
### Client side storage
Client-side storage is an excellent way to
- quickly add performance gains to an application
- skip fetching information from the server 
- useful when offline

Client-side storage can be done with 
- cookies 
- local Storage 
- indexedDB
- WebSQL (a deprecated method that should not be used in new projects)

---
### Local Storage
Local Storage
- the simplest of the storage mechanisms
- uses a key/value system for storing data
- only String (JSON)
- appropriate for smaller sets of data 
- larger data with more complex storage needs would be better stored typically in IndexedDB

---
### Example
```js
<div id="app">
  My name is <input v-model="name">
</div>

const app = new Vue({
  el: '#app',
  data: { name: '' },
  mounted() {
    if (localStorage.name) {
      this.name = localStorage.name;
    }
  },
  watch: {
    name(newName) {
      localStorage.name = newName;
    }
  }
});
```


---
### More Complex Example
Immediately writing the value may not be advisable. 
Let’s consider a slightly more advanced example. 

```js
<div id="app">
  <p>
    My name is <input v-model="name">
    and I am <input v-model="age"> years old.
  </p>
  <p>
    <button @click="persist">Save</button>
  </p>
</div>
const app = new Vue({
  el: '#app',
  data: { name: '',  age: 0 },
  mounted() {
    if (localStorage.name) {
      this.name = localStorage.name;
    }
    if (localStorage.age) {
      this.age = localStorage.age;
    }
  },
  methods: {
    persist() {
      localStorage.name = this.name;
      localStorage.age = this.age;
      console.log('now pretend I did more stuff...');
    }
  }
})
```

---
### Working with Complex Values
Local Storage only works with simple values. To store more complex values, like objects or arrays, you must serialize and deserialize the values with JSON. 

Here is a more advanced example 
```js
<div id="app">
  <h2>Cats</h2>
  <div v-for="(cat, n) in cats">
    <p>
      <span class="cat">{{ cat }}</span>
      <button @click="removeCat(n)">Remove</button>
    </p>
  </div>
  <p> <input v-model="newCat"><button @click="addCat">Add Cat</button></p>
</div>


const app = new Vue({
  el: '#app',
  data: {
    cats: [],
    newCat: null
  },
  mounted() {
    if (localStorage.getItem('cats')) {
      try {
        this.cats = JSON.parse(localStorage.getItem('cats'));
      } catch(e) {
        localStorage.removeItem('cats');
      }
    }
  },
  methods: {
    addCat() {
      // ensure they actually typed something
      if (!this.newCat) { return; }
      this.cats.push(this.newCat);
      this.newCat = '';
      this.saveCats();
    },
    removeCat(x) {
      this.cats.splice(x, 1);
      this.saveCats();
    },
    saveCats() {
      const parsed = JSON.stringify(this.cats);
      localStorage.setItem('cats', parsed);
    }
  }
})
```

---
### Alternative Patterns
While the Local Storage API is relatively simple, it is missing some basic features that would be useful in many applications. The following plugins wrap Local Storage access and make it easier to use, while also adding functionality like default values.

- vue-local-storage
- vue-reactive-storage
- vue2-storage



---
<!-- .slide: data-background="url('images/demo.jpg')" data-background-size="cover" --> 
<!-- .slide: class="lab" -->
## Demo time!
Local Storage

---

<!-- .slide: data-background="url('images/lab2.jpg')" data-background-size="cover"  --> 
<!-- .slide: class="lab" -->
## Lab time!
Persist Data Locally
