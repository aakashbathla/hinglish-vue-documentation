# Custom Directives {#custom-directives}

<script setup>
const vHighlight = {
  mounted: el => {
    el.classList.add('is-highlight')
  }
}
</script>

<style>
.vt-doc p.is-highlight {
  margin-bottom: 0;
}

.is-highlight {
  background-color: yellow;
  color: black;
}
</style>

## Introduction {#introduction}

Core ke default directives (jaise `v-model` ya `v-show`) ke alawa, Vue aapko apne khud ke custom directives banane ki bhi facility deta hai.

Vue me humne do tarike ke code reuse dekhe hain: [components](/guide/essentials/component-basics) aur [composables](./composables). Components basic building blocks hote hain, aur composables ka focus stateful logic ko reuse karne par hota hai. Custom directives ka use mainly tab hota hai jab aapko plain elements par low-level DOM access se related logic reuse karna ho.

Ek custom directive ko ek object ke form me define kiya jata hai jisme lifecycle hooks hote hain (component ke lifecycle hooks ke similar). Ye hooks us element ko receive karte hain jiske sath directive bind hota hai. Example: ek directive jo DOM me insert hote hi element ko ek class add kar de:

<div class="composition-api">

```vue
<script setup>
// enables v-highlight in templates
const vHighlight = {
  mounted: (el) => {
    el.classList.add('is-highlight')
  }
}
</script>

<template>
  <p v-highlight>This sentence is important!</p>
</template>
```

</div>

<div class="options-api">

```js
const highlight = {
  mounted: (el) => el.classList.add('is-highlight')
}

export default {
  directives: {
    // enables v-highlight in template
    highlight
  }
}
```

```vue-html
<p v-highlight>This sentence is important!</p>
```

</div>

<div class="demo">
  <p v-highlight>This sentence is important!</p>
</div>

<div class="composition-api">

`<script setup>` me, koi bhi camelCase variable jo `v` prefix se start hota hai, use custom directive ke roop me use kiya ja sakta hai. Upar wale example me, `vHighlight` ko template me `v-highlight` ke roop me use kar sakte ho.

Agar aap `<script setup>` use nahi kar rahe ho, toh custom directives ko `directives` option ke through register kiya ja sakta hai:

```js
export default {
  setup() {
    /*...*/
  },
  directives: {
    // enables v-highlight in template
    highlight: {
      /* ... */
    }
  }
}
```

</div>

<div class="options-api">

Components ki tarah, custom directives ko bhi register karna zaroori hai taaki unhe templates me use kiya ja sake. Upar wale example me, humne local registration `directives` option ke through ki hai.

</div>

Ye bhi common hai ki custom directives ko globally app level par register kiya jaye:

```js
const app = createApp({})

// make v-highlight usable in all components
app.directive('highlight', {
  /* ... */
})
```

## Kab use karein custom directives {#when-to-use}

Custom directives tabhi use karni chahiye jab aapko aisi functionality chahiye jo sirf direct DOM manipulation ke through possible ho.

Iska ek common example hai `v-focus` custom directive jo element ko focus me le aata hai.

<div class="composition-api">

```vue
<script setup>
// enables v-focus in templates
const vFocus = {
  mounted: (el) => el.focus()
}
</script>

<template>
  <input v-focus />
</template>
```

</div>

<div class="options-api">

```js
const focus = {
  mounted: (el) => el.focus()
}

export default {
  directives: {
    // enables v-focus in template
    focus
  }
}
```

```vue-html
<input v-focus />
```

</div>

Yeh directive `autofocus` attribute se zyada useful hai kyunki yeh sirf page load par hi nahi balki jab element ko Vue dynamically insert karta hai tab bhi kaam karta hai!

Declarative templating ke saath built-in directives jaise `v-bind` use karna recommended hai jab bhi possible ho, kyunki yeh zyada efficient aur server-rendering friendly hote hain.

## Directive Hooks {#directive-hooks}

Ek directive definition object multiple hook functions provide kar sakta hai (sab optional hote hain):

```js
const myDirective = {
  // called before bound element's attributes
  // or event listeners are applied
  created(el, binding, vnode) {
    // see below for details on arguments
  },
  // called right before the element is inserted into the DOM.
  beforeMount(el, binding, vnode) {},
  // called when the bound element's parent component
  // and all its children are mounted.
  mounted(el, binding, vnode) {},
  // called before the parent component is updated
  beforeUpdate(el, binding, vnode, prevVnode) {},
  // called after the parent component and
  // all of its children have updated
  updated(el, binding, vnode, prevVnode) {},
  // called before the parent component is unmounted
  beforeUnmount(el, binding, vnode) {},
  // called when the parent component is unmounted
  unmounted(el, binding, vnode) {}
}
```

### Hook Arguments {#hook-arguments}

Directive hooks ko yeh arguments diye jaate hain:

- `el`: woh element jiske saath directive bound hai. Isse directly DOM manipulate kiya ja sakta hai.

- `binding`: ek object jisme following properties hoti hain:

  - `value`: Directive ko jo value pass ki gayi hai. Jaise `v-my-directive="1 + 1"` me value `2` hogi.
  - `oldValue`: Pichhli value, sirf `beforeUpdate` aur `updated` me available hoti hai. Yeh available hoti hai chahe value change hui ho ya nahi.
  - `arg`: Argument jo directive ko pass kiya gaya hai (agar koi hai). Jaise `v-my-directive:foo` me arg `"foo"` hoga.
  - `modifiers`: Ek object jo modifiers contain karta hai (agar diye gaye hain). Jaise `v-my-directive.foo.bar` me modifiers object `{ foo: true, bar: true }` hoga.
  - `instance`: Component instance jisme directive use ho raha hai.
  - `dir`: Directive definition object.

- `vnode`: woh underlying VNode jo bound element ko represent karta hai.
- `prevVnode`: woh VNode jo pichhle render se bound element ko represent karta hai. Sirf `beforeUpdate` aur `updated` hooks me available hota hai.

Example ke liye, yeh directive usage consider karo:

```vue-html
<div v-example:foo.bar="baz">
```

`binding` argument ek object hoga is shape me:

```js
{
  arg: 'foo',
  modifiers: { bar: true },
  value: /* value of `baz` */,
  oldValue: /* value of `baz` from previous update */
}
```

Built-in directives ki tarah hi, custom directive arguments bhi dynamic ho sakte hain.  
For example:

```vue-html
<div v-example:[arg]="value"></div>
```

Yahan directive ka argument reactively update hoga based on `arg` property jo humare component state me hai.

:::tip Note
`el` ke alawa, aapko in arguments ko read-only treat karna chahiye aur kabhi modify nahi karna chahiye. Agar aapko hooks ke beech information share karni ho, toh recommend hai ki aap element ke [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset) ka use karein.
:::

## Function Shorthand {#function-shorthand}

Aksar custom directive ka behavior `mounted` aur `updated` dono hooks ke liye same hota hai, aur baaki hooks ki zarurat nahi hoti. Aise cases me hum directive ko ek simple function ke roop me define kar sakte hain:

```vue-html
<div v-color="color"></div>
```

```js
app.directive('color', (el, binding) => {
  // this will be called for both `mounted` and `updated`
  el.style.color = binding.value
})
```

## Object Literals {#object-literals}

Agar aapki directive ko multiple values ki zarurat hai, toh aap ek JavaScript object literal bhi pass kar sakte ho. Dhyaan rahe, directives kisi bhi valid JavaScript expression ko accept kar sakti hain.

```vue-html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

```js
app.directive('demo', (el, binding) => {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text) // => "hello!"
})
```

## Usage on Components {#usage-on-components}

:::warning Not recommended
Components par custom directives use karna recommended nahi hai. Agar ek component ke multiple root nodes ho, toh unexpected behavior aa sakta hai.
:::

Jab components par use kiya jata hai, custom directives hamesha component ke root node par apply hoti hain, bilkul waise hi jaise [Fallthrough Attributes](/guide/components/attrs).

```vue-html
<MyComponent v-demo="test" />
```

```vue-html
<!-- template of MyComponent -->

<div> <!-- v-demo directive will be applied here -->
  <span>My component content</span>
</div>
```

Note karein ki components ke paas kabhi-kabhi ek se zyada root nodes ho sakte hain. Agar directive ek multi-root component par apply kiya jaye, toh wo ignore ho jayegi aur ek warning throw hogi. Attributes ke opposite, directives ko kisi aur element par `v-bind="$attrs"` ke through pass nahi kiya ja sakta.
