# Async Components {#async-components}

## Basic Usage {#basic-usage}

Badi applications mein, humein app ko chhote-chhote parts mein todna padta hai aur zarurat padne par hi kisi component ko server se load karna hota hai. Vue mein yeh kaam `defineAsyncComponent` function se hota hai:

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => {
  return new Promise((resolve, reject) => {
    // ...component ko server se load karo
    resolve(/* loaded component */)
  })
})
// ... `AsyncComp` ko ek normal component ki tarah use kar sakte ho
```

Jaise ki dikh raha hai, `defineAsyncComponent` ek loader function leta hai jo ek Promise return karta hai. Jab component load ho jaye to `resolve` call karte hain, aur agar fail ho jaye to `reject(reason)`.

[ES module dynamic import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) bhi ek Promise return karta hai, isliye aksar hum usse `defineAsyncComponent` ke saath use karte hain. Vite aur webpack jaise bundlers is syntax ko samajhte hain (aur use karte hain bundle splitting ke liye), isliye Vue SFCs ko lazy load karne ke liye isse use kiya ja sakta hai:

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
)
```

`AsyncComp` ek wrapper component ban jaata hai jo loader function ko tabhi call karta hai jab wo page par render hota hai. Saare props aur slots automatically andar pass ho jaate hain, to async wrapper original component ke jaise behave karta hai par lazy loading ke sath.

Jaise normal components, async components ko bhi [globally register](/guide/components/registration#global-registration) kiya ja sakta hai `app.component()` ke saath:

```js
app.component(
  'MyComponent',
  defineAsyncComponent(() => import('./components/MyComponent.vue'))
)
```

<div class="options-api">

Aap `defineAsyncComponent` ka use [local registration](/guide/components/registration#local-registration) mein bhi kar sakte ho:

```vue
<script>
import { defineAsyncComponent } from 'vue'

export default {
  components: {
    AdminPage: defineAsyncComponent(() =>
      import('./components/AdminPageComponent.vue')
    )
  }
}
</script>

<template>
  <AdminPage />
</template>
```

</div>

<div class="composition-api">

Aap async components ko unke parent component ke andar bhi define kar sakte ho:

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

const AdminPage = defineAsyncComponent(() =>
  import('./components/AdminPageComponent.vue')
)
</script>

<template>
  <AdminPage />
</template>
```

</div>

## Loading and Error States {#loading-and-error-states}

Async operations mein loading aur error states to hoti hi hain - `defineAsyncComponent()` in states ko handle karne ke options bhi deta hai:

```js
const AsyncComp = defineAsyncComponent({
  // component load karne wala function
  loader: () => import('./Foo.vue'),

  // Jab component load ho raha ho, tab yeh dikhayega
  loadingComponent: LoadingComponent,
  // Loading component dikhane se pehle kitni delay ho. Default: 200ms
  delay: 200,

  // Agar load fail ho jaye to yeh dikhayega
  errorComponent: ErrorComponent,
  // Kitne time ke baad timeout ho jaye. Default: Infinity
  timeout: 3000
})
```

Agar aap loading component provide karte ho, to wo pehle dikhaya jaayega jab tak actual component load nahi ho jaata. 200ms ka default delay hota hai taaki fast networks mein flickering effect na ho.

Agar Promise reject ho jaata hai, to error component dikhaya jaayega. Aur agar request bahut der tak pending ho to `timeout` ke baad bhi error component dikh sakta hai.

## Lazy Hydration <sup class="vt-badge" data-text="3.5+" /> {#lazy-hydration}

> Yeh section sirf tab apply hota hai jab aap [Server-Side Rendering](/guide/scaling-up/ssr) use kar rahe ho.

Vue 3.5+ mein async components specify kar sakte hain ki wo kab hydrate honge ek hydration strategy provide karke.

- Vue kuch built-in hydration strategies provide karta hai. Yeh individually import karni padti hain taaki unused strategies tree-shake ho sakein.

- Yeh intentionally low-level design hai flexibility ke liye. Future mein syntax sugar ya Nuxt jaise frameworks ke through ispe high-level layer banayi ja sakti hai.

### Hydrate on Idle {#hydrate-on-idle}

`requestIdleCallback` ke through hydrate karta hai:

```js
import { defineAsyncComponent, hydrateOnIdle } from 'vue'

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: hydrateOnIdle(/* optional: max timeout de sakte ho */)
})
```

### Hydrate on Visible {#hydrate-on-visible}

Jab element(s) visible hote hain `IntersectionObserver` ke through, tab hydrate karta hai.

```js
import { defineAsyncComponent, hydrateOnVisible } from 'vue'

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: hydrateOnVisible()
})
```

Aap optionally observer ke liye options object bhi de sakte ho:

```js
hydrateOnVisible({ rootMargin: '100px' })
```

### Hydrate on Media Query {#hydrate-on-media-query}

Jab specified media query match karti hai, tab hydrate hota hai.

```js
import { defineAsyncComponent, hydrateOnMediaQuery } from 'vue'

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: hydrateOnMediaQuery('(max-width:500px)')
})
```

### Hydrate on Interaction {#hydrate-on-interaction}

Jab specified event(s) trigger hote hain component ke element(s) par, tab hydrate hota hai. Jo event hydration ko trigger karta hai, wo hydrate hone ke baad replay bhi hota hai.

```js
import { defineAsyncComponent, hydrateOnInteraction } from 'vue'

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: hydrateOnInteraction('click')
})
```

Multiple event types ki list bhi pass kar sakte ho:

```js
hydrateOnInteraction(['wheel', 'mouseover'])
```

### Custom Strategy {#custom-strategy}

```ts
import { defineAsyncComponent, type HydrationStrategy } from 'vue'

const myStrategy: HydrationStrategy = (hydrate, forEachElement) => {
  // forEachElement ek helper hai jo non-hydrated DOM ke saare root elements par loop karta hai
  // root ek single element ke bajaye fragment bhi ho sakta hai
  forEachElement((el) => {
    // yahan apna logic likh sakte ho
  })
  // jab ready ho jao, to `hydrate` call karo
  hydrate()
  return () => {
    // agar zarurat ho to teardown function return karo
  }
}

const AsyncComp = defineAsyncComponent({
  loader: () => import('./Comp.vue'),
  hydrate: myStrategy
})
```

## Using with Suspense {#using-with-suspense}

Async components ko built-in `<Suspense>` component ke sath use kiya ja sakta hai. `<Suspense>` aur async components ke interaction ka detail explanation [dedicated `<Suspense>` chapter](/guide/built-ins/suspense) mein milta hai.
