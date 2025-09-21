# State Management {#state-management}

## What is State Management? {#what-is-state-management}

Technically, har Vue component instance apna khud ka reactive state "manage" karta hai. Ek simple counter component ka example lo:

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

// state
const count = ref(0)

// actions
function increment() {
  count.value++
}
</script>

<!-- view -->
<template>{{ count }}</template>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  // state
  data() {
    return {
      count: 0
    }
  },
  // actions
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>

<!-- view -->
<template>{{ count }}</template>
```

</div>

It is a self-contained unit with the following parts:

- **State**, jo hamare app ko chalane ka source of truth hai;
- **View**, ek declarative mapping of the **state**;
- **Actions**, jo possible ways hai state ko change karne ke, user inputs ke reaction me jo **view** se aate hain.

Ye ek simple representation hai "one-way data flow" concept ka:

<p style="text-align: center">
  <img alt="state flow diagram" src="./images/state-flow.png" width="252px" style="margin: 40px auto">
</p>

Lekin, ye simplicity breakdown hone lagti hai jab **multiple components ko ek hi common state share karna ho**:

1. Multiple views ko ek hi state piece par depend karna padta hai.
2. Different views ke actions ko ek hi state ko mutate karna padta hai.

Case one ke liye, ek workaround ye hai ki shared state ko ek common ancestor component me "lift" kar do, aur phir usko props ke through neeche pass karo. Lekin ye tedious ban jata hai jab component trees deep hierarchies me ho, aur isse ek aur problem aati hai jise kehte hain [Prop Drilling](/guide/components/provide-inject#prop-drilling).

Case two ke liye, aksar hume direct parent/child instances access karni padti hain template refs ke through, ya phir multiple copies of state mutate aur synchronize karni padti hain emitted events ke saath. Dono hi patterns brittle hote hain aur quickly unmaintainable code banate hain.

Ek simpler aur straightforward solution hai shared state ko components se nikal kar ek global singleton me manage karna. Isse hamara component tree ek bada "view" ban jata hai, aur koi bhi component state access kar sakta hai ya actions trigger kar sakta hai, chahe wo tree ke kahin bhi ho!

## Simple State Management with Reactivity API {#simple-state-management-with-reactivity-api}

<div class="options-api">

Options API me, reactive data ko `data()` option ke through declare kiya jata hai. Internally, `data()` se return hone wale object ko reactive banaya jata hai [`reactive()`](/api/reactivity-core#reactive) function ke through, jo ek public API ke roop me bhi available hai.

</div>

Agar aapke paas ek aisi state hai jo multiple instances ke beech share karni hai, toh aap [`reactive()`](/api/reactivity-core#reactive) ka use karke ek reactive object create kar sakte ho, aur phir usko multiple components me import kar sakte ho:

```js
// store.js
import { reactive } from 'vue'

export const store = reactive({
  count: 0
})
```

<div class="composition-api">

```vue
<!-- ComponentA.vue -->
<script setup>
import { store } from './store.js'
</script>

<template>From A: {{ store.count }}</template>
```

```vue
<!-- ComponentB.vue -->
<script setup>
import { store } from './store.js'
</script>

<template>From B: {{ store.count }}</template>
```

</div>
<div class="options-api">

```vue
<!-- ComponentA.vue -->
<script>
import { store } from './store.js'

export default {
  data() {
    return {
      store
    }
  }
}
</script>

<template>From A: {{ store.count }}</template>
```

```vue
<!-- ComponentB.vue -->
<script>
import { store } from './store.js'

export default {
  data() {
    return {
      store
    }
  }
}
</script>

<template>From B: {{ store.count }}</template>
```

</div>

Ab jab bhi `store` object mutate hoga, dono `<ComponentA>` aur `<ComponentB>` apne views ko automatically update karenge – ab hamare paas ek single source of truth hai.

Lekin iska matlab ye bhi hai ki koi bhi component jo `store` ko import karta hai, wo usko apne hisaab se mutate kar sakta hai:

```vue-html{2}
<template>
  <button @click="store.count++">
    From B: {{ store.count }}
  </button>
</template>
```

Jab simple cases me ye kaam karta hai, lekin globally state ko har component se arbitrarily mutate karna long run me maintainable nahi rahega. Isliye, ye ensure karne ke liye ki state-mutating logic bhi centralized ho jaise state khud centralized hai, recommend kiya jata hai ki methods ko store me define karein jin ke names actions ke intention ko express karte ho:

```js{6-8}
// store.js
import { reactive } from 'vue'

export const store = reactive({
  count: 0,
  increment() {
    this.count++
  }
})
```

```vue-html{2}
<template>
  <button @click="store.increment()">
    From B: {{ store.count }}
  </button>
</template>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNrNkk1uwyAQha8yYpNEiUzXllPVrtRTeJNSqtLGgGBsVbK4ewdwnT9FWWSTFczwmPc+xMhqa4uhl6xklRdOWQQvsbfPrVadNQ7h1dCqpcYaPp3pYFHwQyteXVxKm0tpM0krnm3IgAqUnd3vUFIFUB1Z8bNOkzoVny+wDTuNcZ1gBI/GSQhzqlQX3/5Gng81pA1t33tEo+FF7JX42bYsT1BaONlRguWqZZMU4C261CWMk3EhTK8RQphm8Twse/BscoUsvdqDkTX3kP3nI6aZwcmdQDUcMPJPabX8TQphtCf0RLqd1csxuqQAJTxtYnEUGtIpAH4pn1Ou17FDScOKhT+QNAVM)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNrdU8FqhDAU/JVHLruyi+lZ3FIt9Cu82JilaTWR5CkF8d8bE5O1u1so9FYQzAyTvJnRTKTo+3QcOMlIbpgWPT5WUnS90gjPyr4ll1jAWasOdim9UMum3a20vJWWqxSgkvzTyRt+rocWYVpYFoQm8wRsJh+viHLBcyXtk9No2ALkXd/WyC0CyDfW6RVTOiancQM5ku+x7nUxgUGlOcwxn8Ppu7HJ7udqaqz3SYikOQ5aBgT+OA9slt9kasToFnb5OiAqCU+sFezjVBHvRUimeWdT7JOKrFKAl8VvYatdI6RMDRJhdlPtWdQf5mdQP+SHdtyX/IftlH9pJyS1vcQ2NK8ZivFSiL8BsQmmpMG1s1NU79frYA1k8OD+/I3pUA6+CeNdHg6hmoTMX9pPSnk=)

</div>

:::tip
Dhyan dein ki click handler `store.increment()` ko parentheses ke saath use kar raha hai – ye zaroori hai taki method ko sahi `this` context ke saath call kiya ja sake, kyunki ye ek component method nahi hai.
:::

Yahaan hum ek single reactive object ko store ke roop me use kar rahe hain, lekin aap reactive state ko share karne ke liye dusre [Reactivity APIs](/api/reactivity-core) bhi use kar sakte ho jaise `ref()` ya `computed()`, ya phir ek [Composable](/guide/reusability/composables) se global state return karke bhi.

```js
import { ref } from 'vue'

// global state, created in module scope
const globalCount = ref(1)

export function useCount() {
  // local state, created per-component
  const localCount = ref(1)

  return {
    globalCount,
    localCount
  }
}
```

Ye fact ki Vue ka reactivity system component model se alag (decoupled) hai, usse bahut hi flexible banata hai.

## SSR Considerations {#ssr-considerations}

Agar aap ek application bana rahe ho jo [Server-Side Rendering (SSR)](./ssr) use karti hai, toh upar wala pattern issues create kar sakta hai kyunki store ek singleton hai jo multiple requests ke beech share hota hai. Iske baare me [aur details](./ssr#cross-request-state-pollution) SSR guide me di gayi hain.

## Pinia {#pinia}

Jab humara hand-rolled state management solution simple scenarios ke liye kaam aata hai, bade scale ke production applications me aur bhi cheezein dhyaan me rakhni padti hain:

- Team collaboration ke liye strong conventions
- Vue DevTools ke saath integration, jisme timeline, in-component inspection, aur time-travel debugging shamil ho
- Hot Module Replacement
- Server-Side Rendering support

[Pinia](https://pinia.vuejs.org) ek state management library hai jo ye sab provide karti hai. Ye Vue core team dwara maintain ki jaati hai aur Vue 2 aur Vue 3 dono ke saath kaam karti hai.

Jo users pehle se familiar hain [Vuex](https://vuex.vuejs.org/) se, unke liye — Vuex pehle Vue ki official state management library thi. Ab Pinia ecosystem me wahi role play kar rahi hai, aur Vuex ab maintenance mode me hai. Vuex ab bhi kaam karta hai, lekin naye features nahi aayenge. Naye applications ke liye Pinia use karna recommended hai.

Pinia ek exploration ke roop me start hua tha ki Vuex ka next version (Vuex 5) kaisa dikh sakta hai. Isme Vuex 5 ke liye core team discussions se liye gaye ideas implement kiye gaye. Aakhir me, hume realize hua ki Pinia already unme se zyada cheezein implement karta hai jo hum Vuex 5 me chahte the, aur fir ise hi naye recommendation ke roop me choose kiya gaya.

Vuex ke comparison me, Pinia ek simpler API provide karta hai jisme kam boilerplate hota hai, Composition API style APIs deta hai, aur sabse important baat, TypeScript ke saath use karte waqt solid type inference support karta hai.
