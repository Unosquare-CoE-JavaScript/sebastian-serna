# VueJS

Vue is a web framework to build single page applications

In order to initialize our application with Vue we need to use the `Vue.createApp()` this returns an object with capabilities to track data and manipulate the DOM

**Mount application into the DOM**

```html
<div id="app"></div>
```

```js
// in the mount method pass the selector in which the app is going to render
Vue.createApp({
	// insert the data for the scope of the application
	data() {
		return {
			firstName: 'John'
		}
	}
}).mount('#app')
```

**Vue** allows us to embed expressions into our document

```html
<div id="app">
	<!-- We can use the expression to render a value on the page -->
	{{ 2 + 2 }}
	<!-- data in the scope firstName holds the value 'John' -->
	{{ firstName }}
</div>
```
**Accessing data and methods: ** Vue proxifies its data into the instance object that allows direct access to the data and methods we define

```js
Vue.createApp({
	data() { return {
		firstName: 'John',
		lastName: 'Doe',
	} },
	methods: {
		fullName() {
			// we can use the this keyword due to proxification
			`${this.firstName} ${this.lastName}`;
		}
	}
}).mount('#app');
```

```html
<div id="app">
	{{ fullName() }}
</div>
```

## Directives

Directives are attributes that add behavior to the elements, these directives are defined by Vue

```html
<!-- Vue directives start with a v follow by hyphen -->
<div v-cloack>{{ fullName() }}</div>
```

**Two-way data binding**

This feature allows us to update data in a bidirectional way from the document and the app and viceversa

```js
<label>First Name</label>
<!-- v-model allows us to bind the properties -->
<input type="text" v-model="firstName" />
```

**Binding attributes**

Vue provides a wrapper directive to use values of our app in html attributes

```html
<!-- url is a property data defined in our app -->
<a v-bind:href="url">My anchor</a>

<!-- shorthand -->
<a :href="url">My anchor</a>
```

**Events**

```js
Vue.createApp({
	data() { return {
		age: 1,
	} },
	methods: {
		increment() {
			this.age++;
		}
	}
}).mount();
```

```html
<!-- listen to events with the v-on: follow by the supported event -->
<button v-on:click="increment">Increment</button>
<button v-on:click="age--">Decrement</button>

<!-- Shorthand using @ -->
<button @click="increment">Increment</button>
```

**Passing on data with events**

```js
Vue.createApp({
	data() { return {
		lastName: 'Doe',
	} },
	methods: {
		updateLastName(msg, event) {
			event.preventDefault();
			this.lastName = event.target.value;
		}
	}
}).mount();
```

```html
<!-- $event is provided by vue in our template when listening to events -->
<input type="text" @input="updateLastName('My message', $event)">
```

**Computed properties** 

A computed property is essentially a property defined with **getter/setter functions**

```js
Vue.createApp({
	data() { return {
		firstName: 'John',
		lastName: 'Doe',
	} },
	// the computed property optimizes rendering values with side effects
	computed: {
		fullName() {
			return this.firstName + this.lastName;
		}
	}
}).mount();
```

```html
<!-- With computed properties you don't need to use parens when calling the function -->
<p>{{ fullName }}</p>
```

**Watchers**

Watcher is a special feature that allows one to watch a component and perform specified actions when the value of the component changes.

```js
Vue.createApp({
	data() { return {
		age: 1,
	} },
	watch: {
		age(newVal, oldVal) {
			// side effects to watch
			console.log(newVal, oldVal);
		}
	}
}).mount();
```

**Binding classes**

To pass dynamic values to a class attribute we need to use the `v-bind:class` directive
```js
Vue.createApp({
	data() { 
		return {
			isPurple: false,
			size: 150,
		}
	}
}).mount();
```
```html
<div v-bind:class="{ purple: isPurple }"><div>
<!-- Shorthand -->
<div :class="{ purple: isPurple }"><div>
```

**Binding styles**

As class binding Vue also allows us to pass dynamic values to a style attribute with the following directive `v-bind:style`

```html
<input type="number" v-bind:style="{ width: size + 'px'}" />
```

**Conditional rendering**

With vue we can display or remove elements based on conditions with the `v-if` `v-else-if` `v-else` directives

```js
Vue.createApp({
	data() { 
		return {
			mode: 1,
		}
	}
}).mount();
```
```html
<p v-if="mode == 0">I will not render<p>
<div v-if="mode === 1">I will render<div>
```

**List rendering**

Vue allows us to render multiple elements based on list values with the `v-for` directive

```js
Vue.createApp({
	data() { 
		return {
			birds: ['Pigeons', 'Eagles', 'Doves'],
		}
	}
}).mount();
```
```html
<ul>
	<li v-for="bird in birds">{{birds}}</li>
</ul>
```

# Lifecycle hooks

Each Vue component instance goes through a series of initialization steps when it's created. Along the way, it also runs functions called lifecycle hooks, giving users the opportunity to add their own code at specific stages.

**Hooks**
`beforeCreate`
`created`
`beforeMount`
`mounted`
`beforeUpdate`
`updated`
`beforeUnmount`
`unmounted`

```js
Vue.createApp({
	data() {
		return {};
	},
	// lifecycle hooks
	beforeCreate() {
		console.log('beforeCreate() function called!');
	},
}).mount('#app');
```

**Virtual DOM**

It is a **“virtual”, representation of a UI** which is kept in memory and synced with the **“real” DOM.**

**Components**

Components are pieces that build up your application

```js
const vm = Vue.createApp({});

// create the component before mounting the app
vm.component('blog-post', {
	template: `<h1>{{ message }}</h1>`,
	data() {
		return {
			message: 'I am a component',
		}
	}
});

vm.mount('#app')
```
```html
<div id="app">
	<blog-post></blog-post>
</div>
```

**Vue CLI**

In order to scaffold a Vue project we can use the Vue CLI to generate our project structure by running the following command `npm init vue@lastest`
