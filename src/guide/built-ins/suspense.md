---
outline: deep
---

# Suspense {#suspense}

:::warning Experimental Feature
`<Suspense>` ek experimental feature hai. Iski guarantee nahi hai ki ye stable status tak pahunch payega, aur API usse pehle change ho sakti hai.  
:::

`<Suspense>` ek built-in component hai jo component tree me async dependencies ko orchestrate karne ke liye use hota hai. Ye ek loading state render kar sakta hai jab tak nested async dependencies resolve nahi ho jaati.

## Async Dependencies {#async-dependencies}

Samjhane ke liye ki `<Suspense>` kaunsi problem solve karta hai aur async dependencies ke sath kaise interact karta hai, socho ek component hierarchy kuch is tarah ki hai:

```
<Suspense>
└─ <Dashboard>
   ├─ <Profile>
   │  └─ <FriendStatus> (component with async setup())
   └─ <Content>
      ├─ <ActivityFeed> (async component)
      └─ <Stats> (async component)
```

Component tree me multiple nested components ho sakte hain jinka rendering kisi async resource ke resolve hone par depend karta hai. `<Suspense>` ke bina, har ek component ko apna khud ka loading / error aur loaded state handle karna padta hai. Worst case me, page par teen alag loading spinners dikh sakte hain aur content alag-alag time par load hoga.

`<Suspense>` component hume yeh ability deta hai ki hum top-level loading / error states dikha saken jab tak nested async dependencies resolve nahi ho jaati.

Do types ki async dependencies hain jin par `<Suspense>` wait kar sakta hai:

1. Components jinke `setup()` hook async hote hain. Isme wo components bhi aate hain jo `<script setup>` use karte hain aur unme top-level `await` expressions hote hain.
2. [Async Components](/guide/components/async).

### `async setup()` {#async-setup}

Ek Composition API component ka `setup()` hook async ho sakta hai:

```js
export default {
  async setup() {
    const res = await fetch(...)
    const posts = await res.json()
    return {
      posts
    }
  }
}
```

Agar aap `<script setup>` use kar rahe ho, to top-level `await` expressions ka hona automatically component ko ek async dependency bana deta hai:

```vue
<script setup>
const res = await fetch(...)
const posts = await res.json()
</script>

<template>
  {{ posts }}
</template>
```

### Async Components {#async-components}

Async components by default **"suspensible"** hote hain. Matlab agar parent chain me `<Suspense>` hai, to wo component us `<Suspense>` ka ek async dependency treat hoga. Is case me loading state `<Suspense>` control karega, aur component ke apne loading, error, delay aur timeout options ignore ho jayenge.

Async component `Suspense` ke control se opt-out bhi kar sakta hai aur apni loading state hamesha khud handle kar sakta hai, iske liye options me `suspensible: false` specify karna hota hai.

## Loading State {#loading-state}

`<Suspense>` component ke do slots hote hain: `#default` aur `#fallback`. Dono slots me sirf **ek** immediate child node allowed hai. Agar possible ho to default slot ka node dikhaya jata hai. Agar nahi, to fallback slot ka node render hota hai.

```vue-html
<Suspense>
  <!-- component with nested async dependencies -->
  <Dashboard />

  <!-- loading state via #fallback slot -->
  <template #fallback>
    Loading...
  </template>
</Suspense>
```

Initial render me, `<Suspense>` apna default slot content memory me render karta hai. Agar is process me koi async dependencies milti hain, to ye **pending** state me chala jata hai. Pending state ke dauraan fallback content dikhaya jata hai. Jab saari async dependencies resolve ho jaati hain, `<Suspense>` **resolved** state me aata hai aur resolved default slot content dikhata hai.

Agar initial render ke time pe koi async dependencies nahi mili, to `<Suspense>` directly resolved state me चला jata hai.

Ek baar resolved state me aane ke baad, `<Suspense>` sirf tabhi pending state me wapas jayega jab `#default` slot ka root node replace hoga. Tree ke andar naye nested async dependencies aane par `<Suspense>` pending state me wapas **nahi** jayega.

Jab revert hota hai, fallback content turant display nahi hota. Uski jagah `<Suspense>` purana `#default` content dikhata hai jab tak naya content aur uski async dependencies resolve nahi ho jaati. Ye behavior `timeout` prop se configure kiya ja sakta hai: agar naye default content ko render hone me `timeout` se zyada time lagta hai, to `<Suspense>` fallback content dikhana start karega. Agar `timeout` ka value `0` hai, to default content replace hone par fallback content turant dikh jayega.

## Events {#events}

`<Suspense>` component 3 events emit karta hai: `pending`, `resolve` aur `fallback`.

- `pending` event tab trigger hota hai jab component pending state me jata hai.
- `resolve` event tab emit hota hai jab default slot ka naya content resolve ho jata hai.
- `fallback` event tab fire hota hai jab fallback slot ka content dikhaya jata hai.

Ye events use kiye ja sakte hain, for example, purane DOM ke upar ek loading indicator dikhane ke liye jab naye components load ho rahe ho.

## Error Handling {#error-handling}

`<Suspense>` abhi khud se error handling provide nahi karta. Lekin aap [`errorCaptured`](/api/options-lifecycle#errorcaptured) option ya [`onErrorCaptured()`](/api/composition-api-lifecycle#onerrorcaptured) hook use karke parent component me async errors ko capture aur handle kar sakte ho.

## Combining with Other Components {#combining-with-other-components}

Aksar `<Suspense>` ko [`<Transition>`](./transition) aur [`<KeepAlive>`](./keep-alive) components ke sath use kiya jata hai. Inka nesting order sahi hona bahut zaroori hai taki ye sab sahi tarike se kaam karein.

Inke alawa, ye components aksar [Vue Router](https://router.vuejs.org/) ke `<RouterView>` component ke sath bhi use hote hain.

Niche diya gaya example dikhata hai ki kaise in components ko nest karna chahiye taki sab expected tarike se behave karein. Agar simpler combination chahiye, to unnecessary components hata sakte ho:

```vue-html
<RouterView v-slot="{ Component }">
  <template v-if="Component">
    <Transition mode="out-in">
      <KeepAlive>
        <Suspense>
          <!-- main content -->
          <component :is="Component"></component>

          <!-- loading state -->
          <template #fallback>
            Loading...
          </template>
        </Suspense>
      </KeepAlive>
    </Transition>
  </template>
</RouterView>
```

Vue Router me [lazily loading components](https://router.vuejs.org/guide/advanced/lazy-loading.html) ke liye built-in support hai jo dynamic imports ka use karta hai. Ye async components se alag hote hain aur abhi ye `<Suspense>` ko trigger nahi karte. Lekin inke descendants me agar async components hote hain, to wo normal tarike se `<Suspense>` trigger kar sakte hain.

## Nested Suspense {#nested-suspense}

- Sirf 3.3+ me supported

Jab humare paas multiple async components hote hain (zyada common hota hai nested ya layout-based routes ke liye), jaise ki:

```vue-html
<Suspense>
  <component :is="DynamicAsyncOuter">
    <component :is="DynamicAsyncInner" />
  </component>
</Suspense>
```

`<Suspense>` ek aisi boundary banata hai jo tree ke niche tak saare async components ko resolve karta hai, jaise expected hai. Lekin jab hum `DynamicAsyncOuter` change karte hain, `<Suspense>` uska sahi se wait karta hai; par jab hum `DynamicAsyncInner` change karte hain, to nested `DynamicAsyncInner` resolve hone tak ek empty node render hota hai (purana content ya fallback slot dikhane ke bajay).

Is problem ko solve karne ke liye, hum nested component ke patch ko handle karne ke liye ek **nested suspense** use kar sakte hain, kuch is tarah:

```vue-html
<Suspense>
  <component :is="DynamicAsyncOuter">
    <Suspense suspensible> <!-- this -->
      <component :is="DynamicAsyncInner" />
    </Suspense>
  </component>
</Suspense>
```

Agar aap `suspensible` prop set nahi karte, to inner `<Suspense>` ko parent `<Suspense>` ke nazariye se ek sync component maana jayega. Iska matlab hai ki uska apna fallback slot hoga, aur agar dono `Dynamic` components ek saath change ho gaye, to child `<Suspense>` apni dependency tree load karte waqt kuch der ke liye empty nodes aur multiple patching cycles dikh sakte hain — jo shayad desirable na ho. Jab `suspensible` set hota hai, to saari async dependency handling (events emit hona bhi) parent `<Suspense>` sambhalta hai, aur inner `<Suspense>` sirf dependency resolution aur patching ke liye ek aur boundary ka kaam karta hai.

---

**Related**

- [`<Suspense>` API reference](/api/built-in-components#suspense)
