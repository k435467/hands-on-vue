# hands-on-vue

Hands-on the tutorial [vue-crash-2021](https://github.com/bradtraversy/vue-crash-2021).

- [hands-on-vue](#hands-on-vue)
  - [Usage](#usage)
  - [----- Vue 2 -----](#------vue-2------)
  - [Introduction](#introduction)
  - [Lifecycle](#lifecycle)
  - [Class and Style Bindings](#class-and-style-bindings)
    - [Binding HTML Classes](#binding-html-classes)
      - [Object Syntax](#object-syntax)
      - [Array Syntax](#array-syntax)
      - [With Components](#with-components)
    - [Binding Inline Styles](#binding-inline-styles)
      - [Object Syntax](#object-syntax-1)
      - [Array Syntax](#array-syntax-1)
  - [List Rendering](#list-rendering)
    - [Array Change Detection](#array-change-detection)
      - [Mutation Methods](#mutation-methods)
      - [Replacing an Array](#replacing-an-array)
  - [Change Detection Caveats](#change-detection-caveats)
    - [For Objects](#for-objects)
    - [For Arrays](#for-arrays)
  - [Event Handling](#event-handling)
    - [$event](#event)
    - [$emit](#emit)
  - [Prop](#prop)
    - [Validation](#validation)

## Usage

```shell
yarn  # install dependencies
yarn backend  # run the json-server
yarn serve  # Compiles and hot-reloads for development
yarn build  # Compiles and minifies for production
yarn lint  # Lints and fixes files
```

## ----- Vue 2 -----

## Introduction

```ts
// vue ts
import Vue from "vue";
export default Vue.extend({
  name: "Square",

  // -----------
  // COMPONENTS
  // -----------

  components: {
    /* ... */
  },

  // ------
  // PROPS
  // ------

  props: {
    a: String,
    b: { type: Function},
  },

  // -----
  // DATA
  // -----

  data: function() {
    return {
      firstName: String,
      lastName: String,
    }
  }

  // ----------
  // LIFECYCLE
  // ----------

  created: function() {
    console.log("created. " + this.a);
  },

  // ---------
  // COMPUTED
  // ---------

  computed: {
    reversedMessage: function() {
      return this.a.split('').reverse().join('');
    },

    // GETTER AND SETTER
    fullName: {
      get: function() {
        return this.firstName + ' ' + this.lastName;
      },
      set: function(newVal) {
        let names = newVal.split(' ');
        this.firstName = names[0];
        this.lastName = names[1];
      }
    },
  },

  // --------
  // WATCHER
  // --------

  watch: {
    a: function(val) {
      this.doSomething();
    }
  }

  // --------
  // METHODS
  // --------

  methods: {
    doSomething: function() {
      console.log("doSomething");
    },
  }

});
```

## Lifecycle

- **beforeCreate**
- **created** : props, data, methods, watch, computed
- **beforeMount** : $el with {{}}
- **mounted** : $el with actual value in {{}}
- **beforeUpdate**
- **updated**
- **beforeDestroy**
- **destroyed**

## Class and Style Bindings

### Binding HTML Classes

#### Object Syntax

v-bind:class co-exist with the plain class attribute.

```html
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

```js
data: {
  isActive: true,
  hasError: false
}
```

with **object**.

```html
<div v-bind:class="classObject"></div>
```

```js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

with **computed**.

```html
<div v-bind:class="classObject"></div>
```

```js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

#### Array Syntax

list of classes.

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

```js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

ternary.

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

object syntax inside array syntax.

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

#### With Components

When using the class attribute on a custom components, those classes will be added the the component's root element. Existing classes on this element will not be overwritten.

declare a component:

```js
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})
```

add some classes:

```js
<my-component class="baz boo"></my-component>
```

render:

```js
<p class="foo bar baz boo">Hi</p>
```

### Binding Inline Styles

#### Object Syntax

```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

bind to a style object directly is better:

```html
<div v-bind:style="styleObject"></div>
```

```js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

#### Array Syntax

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

## List Rendering

### Array Change Detection

#### Mutation Methods

- push
- pop
- shift
- unshift
- splice
- sort
- reverse

#### Replacing an Array

```js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

## Change Detection Caveats

### For Objects

Vue does not allow dynamically adding new root-level reactive properties to an already created instance.

### For Arrays

Vue cannot detect the following changes to an array:

1. When you directly set an item with the index, e.g. `vm.items[indexOfItem] = newValue`
2. When you modify the length of the array, e.g. `vm.items.length = newLength`

To overcome caveat 1:

```js
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
```

```js
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

To overcome caveat 2:

```js
vm.items.splice(newLength)
```

## Event Handling

### $event

We can pass original DOM event into a method using the special `$event` variable:

```html
<button v-on:click="warn('something', $event)">
  Submit
</button>
```

### $emit

inline:

```html
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
```

method:

```js
methods: {
  handleClick() {
    this.$emit('enlarge-text');
  },
},
```

emitting a value with event. we can use $emit‘s 2nd parameter:

```html
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```

## Prop

```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // or any other constructor
}
```

### Validation

```js
Vue.component('my-component', {
  props: {
    // Basic type check (`null` and `undefined` values will pass any type validation)
    propA: Number,
    // Multiple possible types
    propB: [String, Number],
    // Required string
    propC: {
      type: String,
      required: true
    },
    // Number with a default value
    propD: {
      type: Number,
      default: 100
    },
    // Object with a default value
    propE: {
      type: Object,
      // Object or array defaults must be returned from
      // a factory function
      default: function () {
        return { message: 'hello' }
      }
    },
    // Custom validator function
    propF: {
      validator: function (value) {
        // The value must match one of these strings
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```