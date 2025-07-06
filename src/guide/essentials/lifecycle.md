# Lifecycle Hooks {#lifecycle-hooks}

Har Vue component instance jab create hota hai to wo kuch initialization steps se guzarta hai - jaise data observation setup karna, template compile karna, DOM me instance ko mount karna, aur jab data change ho to DOM update karna. Is process ke dauraan, kuch functions run hote hain jine lifecycle hooks kehte hain, jinke through users specific stages pe apna code chala sakte hain.

## Registering Lifecycle Hooks {#registering-lifecycle-hooks}

For example, <span class="composition-api">`onMounted`</span><span class="options-api">`mounted`</span> hook ka use component ke initial render complete hone ke baad aur DOM nodes create hone ke turant baad code run karne ke liye kiya ja sakta hai:

<div class="composition-api">

```vue
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  console.log(`the component is now mounted.`)
})
</script>
```

</div>
<div class="options-api">

```js
export default {
  mounted() {
    console.log(`the component is now mounted.`)
  }
}
```

</div>

Aur bhi kai hooks hote hain jo instance ke lifecycle ke alag-alag stages pe call kiye jaate hain, jismein sabse commonly used hooks hain <span class="composition-api">[`onMounted`](/api/composition-api-lifecycle#onmounted), [`onUpdated`](/api/composition-api-lifecycle#onupdated), aur [`onUnmounted`](/api/composition-api-lifecycle#onunmounted)</span><span class="options-api">[`mounted`](/api/options-lifecycle#mounted), [`updated`](/api/options-lifecycle#updated), aur [`unmounted`](/api/options-lifecycle#unmounted)</span>.

<div class="options-api">

Saare lifecycle hooks call hote hain aise `this` context ke saath jo current active instance ko point karta hai. Iska matlab ye hai ki aapko lifecycle hooks declare karte waqt arrow functions ka use avoid karna chahiye, kyunki agar aap arrow function use karenge to `this` ke through component instance tak access nahi milega.

</div>

<div class="composition-api">

Jab aap `onMounted` call karte ho, Vue automatically registered callback function ko current active component instance ke saath associate kar deta hai. Iska matlab ye hai ki ye hooks **synchronously** component setup ke dauraan hi register hone chahiye. Jaise ki, aise mat karo:

```js
setTimeout(() => {
  onMounted(() => {
    // this won't work.
  })
}, 100)
```

Dhyan rahe iska matlab ye nahi hai ki call ko lexical form mein hamesha `setup()` ya `<script setup>` ke andar hi likhna hoga. `onMounted()` ko kisi external function ke andar bhi call kiya ja sakta hai, bas itna zaroori hai ki call stack synchronous ho aur `setup()` ke andar se originate kare.

</div>

## Lifecycle Diagram {#lifecycle-diagram}

Neeche ek diagram diya gaya hai jo instance lifecycle dikhata hai. Abhi ke liye aapko is diagram ko poori tarah samajhna zaroori nahi hai, lekin jaise-jaise aap aur seekhenge aur banayenge, ye ek useful reference banega.

![Component lifecycle diagram](./images/lifecycle.png)

<!-- https://www.figma.com/file/Xw3UeNMOralY6NV7gSjWdS/Vue-Lifecycle -->

Har lifecycle hook aur unke use cases ke details ke liye consult karein <span class="composition-api">[Lifecycle Hooks API reference](/api/composition-api-lifecycle)</span><span class="options-api">[Lifecycle Hooks API reference](/api/options-lifecycle)</span>.
