<script setup>
import SwitchComponent from './keep-alive-demos/SwitchComponent.vue'
</script>

# KeepAlive {#keepalive}

`<KeepAlive>` ek built-in component hai jo hume conditionally component instances ko cache karne deta hai jab hum dynamically multiple components ke beech switch karte hain.

## Basic Usage {#basic-usage}

Component Basics chapter me, humne [Dynamic Components](/guide/essentials/component-basics#dynamic-components) ka syntax introduce kiya tha, jisme `<component>` special element use hota hai:

```vue-html
<component :is="activeComponent" />
```

By default, jab aap ek active component instance se switch karte ho toh wo unmount ho jata hai. Iska matlab hai ki uske andar jo bhi changed state hai wo lost ho jayegi. Jab ye component dobara display hoga, ek naya instance banega jo sirf initial state ke sath aayega.

Niche diye gaye example me, humare paas do stateful components hain - A me ek counter hai, aur B me ek message hai jo input ke sath `v-model` ke through sync hota hai. Koshish karo ek ka state update karne ki, phir switch karo dusre pe, aur phir wapas aa jao:

<SwitchComponent />

Aap notice karoge ki jab wapas aaye, toh pehle wala changed state reset ho gaya hai.

Switch pe naya component instance create karna normally useful hota hai, lekin is case me hum chahte hain ki dono component instances preserve rahe, even jab wo inactive ho. Is problem ko solve karne ke liye, hum apne dynamic component ko `<KeepAlive>` built-in component ke andar wrap kar sakte hain:

```vue-html
<!-- Inactive components will be cached! -->
<KeepAlive>
  <component :is="activeComponent" />
</KeepAlive>
```

Ab, state component switches ke beech persist (bani) rahegi:

<SwitchComponent use-KeepAlive />

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNqtUsFOwzAM/RWrl4IGC+cqq2h3RFw495K12YhIk6hJi1DVf8dJSllBaAJxi+2XZz8/j0lhzHboeZIl1NadMA4sd73JKyVaozsHI9hnJqV+feJHmODY6RZS/JEuiL1uTTEXtiREnnINKFeAcgZUqtbKOqj7ruPKwe6s2VVguq4UJXEynAkDx1sjmeMYAdBGDFBLZu2uShre6ioJeaxIduAyp0KZ3oF7MxwRHWsEQmC4bXXDJWbmxpjLBiZ7DwptMUFyKCiJNP/BWUbO8gvnA+emkGKIgkKqRrRWfh+Z8MIWwpySpfbxn6wJKMGV4IuSs0UlN1HVJae7bxYvBuk+2IOIq7sLnph8P9u5DJv5VfpWWLaGqTzwZTCOM/M0IaMvBMihd04ruK+lqF/8Ajxms8EFbCiJxR8khsP6ncQosLWnWV6a/kUf2nqu75Fby04chA0iPftaYryhz6NBRLjdtajpHZTWPio=)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNqtU8tugzAQ/JUVl7RKWveMXFTIseofcHHAiawasPxArRD/3rVNSEhbpVUrIWB3x7PM7jAkuVL3veNJmlBTaaFsVraiUZ22sO0alcNedw2s7kmIPHS1ABQLQDEBAMqWvwVQzffMSQuDz1aI6VreWpPCEBtsJppx4wE1s+zmNoIBNLdOt8cIjzut8XAKq3A0NAIY/QNveFEyi8DA8kZJZjlGALQWPVSSGfNYJjVvujIJeaxItuMyo6JVzoJ9VxwRmtUCIdDfNV3NJWam5j7HpPOY8BEYkwxySiLLP1AWkbK4oHzmXOVS9FFOSM3jhFR4WTNfRslcO54nSwJKcCD4RsnZmJJNFPXJEl8t88quOuc39fCrHalsGyWcnJL62apYNoq12UQ8DLEFjCMy+kKA7Jy1XQtPlRTVqx+Jx6zXOJI1JbH4jejg3T+KbswBzXnFlz9Tjes/V/3CjWEHDsL/OYNvdCE8Wu3kLUQEhy+ljh+brFFu)

</div>

:::tip
Jab [in-DOM templates](/guide/essentials/component-basics#in-dom-template-parsing-caveats) use kar rahe ho, tab ise `<keep-alive>` ke naam se reference karna chahiye.  
:::

## Include / Exclude {#include-exclude}

By default, `<KeepAlive>` andar ke saare component instances ko cache karega. Hum is behavior ko `include` aur `exclude` props ke through customize kar sakte hain. Dono props ek comma-separated string, `RegExp`, ya phir array ho sakte hain jisme types diye gaye ho.

```vue-html
<!-- comma-delimited string -->
<KeepAlive include="a,b">
  <component :is="view" />
</KeepAlive>

<!-- regex (use `v-bind`) -->
<KeepAlive :include="/a|b/">
  <component :is="view" />
</KeepAlive>

<!-- Array (use `v-bind`) -->
<KeepAlive :include="['a', 'b']">
  <component :is="view" />
</KeepAlive>
```

Component ka match uske [`name`](/api/options-misc#name) option ke against check hota hai. Matlab jo components `KeepAlive` ke through conditionally cache karne hain, unka `name` option explicitly declare karna zaroori hai.

:::tip
Version 3.2.34 se, agar aap single-file component me `<script setup>` use karte ho to `name` option automatically filename se infer ho jata hai. Matlab manually declare karne ki zaroorat nahi hai.  
:::

## Max Cached Instances {#max-cached-instances}

Hum `max` prop ke through cache hone wale component instances ki maximum limit define kar sakte hain. Jab `max` specify kiya jata hai, to `<KeepAlive>` ek [LRU cache](<https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_recently_used_(LRU)>) ki tarah behave karta hai: agar cached instances ki count `max` se zyada hone wali ho, to sabse purana (least recently accessed) instance destroy kar diya jata hai aur naye instance ke liye jagah banaayi jati hai.

```vue-html
<KeepAlive :max="10">
  <component :is="activeComponent" />
</KeepAlive>
```

## Lifecycle of Cached Instance {#lifecycle-of-cached-instance}

Jab ek component instance DOM se remove hota hai lekin wo `<KeepAlive>` ke cache kiya hua tree ka part hai, tab wo **deactivated** state me chala jata hai instead of unmounted hone ke. Aur jab ek component instance cached tree ka part hote hue wapas DOM me insert hota hai, tab wo **activated** ho jata hai.

<div class="composition-api">

Ek kept-alive component in dono states ke liye lifecycle hooks register kar sakta hai [`onActivated()`](/api/composition-api-lifecycle#onactivated) aur [`onDeactivated()`](/api/composition-api-lifecycle#ondeactivated) ka use karke:

```vue
<script setup>
import { onActivated, onDeactivated } from 'vue'

onActivated(() => {
  // called on initial mount
  // and every time it is re-inserted from the cache
})

onDeactivated(() => {
  // called when removed from the DOM into the cache
  // and also when unmounted
})
</script>
```

</div>
<div class="options-api">

Ek kept-alive component in dono states ke liye lifecycle hooks register kar sakta hai [`activated`](/api/options-lifecycle#activated) aur [`deactivated`](/api/options-lifecycle#deactivated) hooks ka use karke:

```js
export default {
  activated() {
    // called on initial mount
    // and every time it is re-inserted from the cache
  },
  deactivated() {
    // called when removed from the DOM into the cache
    // and also when unmounted
  }
}
```

</div>

Note karo ki:

- <span class="composition-api">`onActivated`</span><span class="options-api">`activated`</span> mount hone par bhi call hota hai, aur <span class="composition-api">`onDeactivated`</span><span class="options-api">`deactivated`</span> unmount hone par call hota hai.

- Ye dono hooks sirf root component (jo `<KeepAlive>` me cache hua hai) ke liye hi nahi, balki cached tree ke descendant components ke liye bhi kaam karte hain.

---

**Related**

- [`<KeepAlive>` API reference](/api/built-in-components#keepalive)
