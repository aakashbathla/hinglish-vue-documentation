# Template Refs {#template-refs}

Jabki Vue ka declarative rendering model aapke liye mostly direct DOM operations ko abstract kar deta hai, kuch cases me aise scenarios ho sakte hain jahan humein underlying DOM elements ka direct access chahiye hota hai. Aise situations me hum special `ref` attribute ka use kar sakte hain:

```vue-html
<input ref="input">
```

`ref` ek special attribute hai, jo `v-for` chapter me discuss kiye gaye `key` attribute ki tarah hota hai. Ye humein ek specific DOM element ya child component instance ka direct reference provide karta hai, jab wo mount ho jaata hai. Ye tab kaam aata hai jab aap chahein ki jaise hi component mount ho, koi input field automatically focus ho jaaye, ya kisi element par koi 3rd party library initialize ki jaaye.

## Accessing the Refs {#accessing-the-refs}

<div class="composition-api">

Composition API ke saath reference ko access karne ke liye, hum [`useTemplateRef()`](/api/composition-api-helpers#usetemplateref) <sup class="vt-badge" data-text="3.5+" /> helper ka use kar sakte hain:

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'

// the first argument must match the ref value in the template
const input = useTemplateRef('my-input')

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="my-input" />
</template>
```

Jab aap TypeScript ka use karte hain, toh Vue ka IDE support aur `vue-tsc` automatically `input.value` ka type infer kar leta hai based on ki `ref` attribute kis element ya component par laga hai.

<details>
<summary>3.5 se pehle ka usage</summary>

Un versions me jo 3.5 se pehle ke hain, jahan `useTemplateRef()` introduce nahi hua tha, wahan humein ek aisa ref declare karna padta tha jiska naam template ke `ref` attribute ke value se match karta ho:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// declare a ref to hold the element reference
// the name must match template ref value
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```

Agar aap `<script setup>` ka use nahi kar rahe hain, toh ensure karein ki aap `ref` ko `setup()` function se return bhi karein:

```js{6}
export default {
  setup() {
    const input = ref(null)
    // ...
    return {
      input
    }
  }
}
```

</details>

</div>
<div class="options-api">

Jo ref aap define karte ho, wo `this.$refs` ke through access hota hai:

```vue
<script>
export default {
  mounted() {
    this.$refs.input.focus()
  }
}
</script>

<template>
  <input ref="input" />
</template>
```

</div>

Dhyan dein ki aap ref ko tabhi access kar sakte hain jab **component mount ho chuka ho.** Agar aap <span class="options-api">`$refs.input`</span><span class="composition-api">`input`</span> ko kisi template expression me access karne ki koshish karte hain, toh ye <span class="options-api">`undefined`</span><span class="composition-api">`null`</span> hoga pehle render par. Aisa isliye hota hai kyunki wo element tab tak exist hi nahi karta jab tak pehla render complete nahi ho jaata!

<div class="composition-api">

Agar aap template ref me hone wale changes ko watch karne ki koshish kar rahe hain, toh ye ensure karein ki aap us case ka bhi dhyan rakhein jahan ref ka value `null` ho sakta hai:

```js
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // not mounted yet, or the element was unmounted (e.g. by v-if)
  }
})
```

See also: [Typing Template Refs](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />

</div>

## Ref on Component {#ref-on-component}

> Ye section maan ke chalta hai ki aapko [Components](/guide/essentials/component-basics) ki knowledge hai. Agar nahi hai toh isse skip karke baad me wapas aa sakte hain.

`ref` ko aap child component par bhi use kar sakte hain. Is case me reference ek component instance ka hoga:

<div class="composition-api">

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'
import Child from './Child.vue'

const childRef = useTemplateRef('child')

onMounted(() => {
  // childRef.value will hold an instance of <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

<details>
<summary>Usage before 3.5</summary>

```vue
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value will hold an instance of <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

</details>

</div>
<div class="options-api">

```vue
<script>
import Child from './Child.vue'

export default {
  components: {
    Child
  },
  mounted() {
    // this.$refs.child will hold an instance of <Child />
  }
}
</script>

<template>
  <Child ref="child" />
</template>
```

</div>

<span class="composition-api">Agar child component Options API ka use kar raha hai ya `<script setup>` ka use nahi kar raha, toh</span><span class="options-api">Referenced instance</span> child component ke `this` ke barabar hoti hai — iska matlab hai parent component child ke har property aur method ko directly access kar sakta hai. Ye parent-child ke beech tightly coupled implementation details banana bahut easy bana deta hai, isliye component refs sirf tabhi use karne chahiye jab zarurat ho — zyadatar cases me aapko props aur emit interfaces ke through parent-child interactions implement karne chahiye.

<div class="composition-api">

Ek exception ye hai ki `<script setup>` use karne wale components **by default private** hote hain: agar parent component kisi `<script setup>` component ko reference karta hai, toh wo kuch bhi access nahi kar paayega jab tak child component `defineExpose` macro ka use karke koi public interface expose nahi karta:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

// Compiler macros, such as defineExpose, don't need to be imported
defineExpose({
  a,
  b
})
</script>
```

Jab parent component is component ka instance template refs ke through access karta hai, toh jo instance milta hai uska shape `{ a: number, b: number }` hoga (refs automatically unwrap ho jaate hain, bilkul normal instances ki tarah).

Dhyan dein ki `defineExpose` ko kisi bhi `await` operation se pehle call karna zaroori hai. Agar aap `await` ke baad koi property ya method expose karte ho, toh wo accessible nahi hogi.

See also: [Typing Component Template Refs](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

</div>
<div class="options-api">

`expose` option ka use child component ke instance access ko limit karne ke liye kiya ja sakta hai:

```js
export default {
  expose: ['publicData', 'publicMethod'],
  data() {
    return {
      publicData: 'foo',
      privateData: 'bar'
    }
  },
  methods: {
    publicMethod() {
      /* ... */
    },
    privateMethod() {
      /* ... */
    }
  }
}
```

Upar wale example me, agar koi parent component is component ko template ref ke through reference karta hai, toh wo sirf `publicData` aur `publicMethod` ko hi access kar paayega.

</div>

## Refs inside `v-for` {#refs-inside-v-for}

> Ye feature v3.5 ya uske baad ke versions me available hai

<div class="composition-api">

Jab `ref` ko `v-for` ke andar use kiya jaata hai, toh us ref ka value ek Array hota hai, jo component ke mount hone ke baad uss loop ke elements se populate hota hai:

```vue
<script setup>
import { ref, useTemplateRef, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = useTemplateRef('items')

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="items">
      {{ item }}
    </li>
  </ul>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNp9UsluwjAQ/ZWRLwQpDepyQoDUIg6t1EWUW91DFAZq6tiWF4oU5d87dtgqVRyyzLw3b+aN3bB7Y4ptQDZkI1dZYTw49MFMuBK10dZDAxZXOQSHC6yNLD3OY6zVsw7K4xJaWFldQ49UelxxVWnlPEhBr3GszT6uc7jJ4fazf4KFx5p0HFH+Kme9CLle4h6bZFkfxhNouAIoJVqfHQSKbSkDFnVpMhEpovC481NNVcr3SaWlZzTovJErCqgydaMIYBRk+tKfFLC9Wmk75iyqg1DJBWfRxT7pONvTAZom2YC23QsMpOg0B0l0NDh2YjnzjpyvxLrYOK1o3ckLZ5WujSBHr8YL2gxnw85lxEop9c9TynkbMD/kqy+svv/Jb9wu5jh7s+jQbpGzI+ZLu0byEuHZ+wvt6Ays9TJIYl8A5+i0DHHGjvYQ1JLGPuOlaR/TpRFqvXCzHR2BO5iKg0Zmm/ic0W2ZXrB+Gve2uEt1dJKs/QXbwePE)

<details>
<summary>Usage before 3.5</summary>

Un versions me jo 3.5 se pehle ke hain, jahan `useTemplateRef()` introduce nahi hua tha, wahan humein ek aisa ref declare karna padta tha jiska naam template ke `ref` attribute ke value se match karta ho. Saath hi, us ref ka value bhi ek array hona chahiye:

```vue
<script setup>
import { ref, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = ref([])

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">
      {{ item }}
    </li>
  </ul>
</template>
```

</details>

</div>
<div class="options-api">

Jab `ref` ko `v-for` ke andar use kiya jaata hai, toh jo ref value milti hai wo ek array hoti hai jismein corresponding elements included hote hain:

```vue
<script>
export default {
  data() {
    return {
      list: [
        /* ... */
      ]
    }
  },
  mounted() {
    console.log(this.$refs.items)
  }
}
</script>

<template>
  <ul>
    <li v-for="item in list" ref="items">
      {{ item }}
    </li>
  </ul>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNpFjk0KwjAQha/yCC4Uaou6kyp4DuOi2KkGYhKSiQildzdNa4WQmTc/37xeXJwr35HEUdTh7pXjszT0cdYzWuqaqBm9NEDbcLPeTDngiaM3PwVoFfiI667AvsDhNpWHMQzF+L9sNEztH3C3JlhNpbaPNT9VKFeeulAqplfY5D1p0qurxVQSqel0w5QUUEedY8q0wnvbWX+SYgRAmWxIiuSzm4tBinkc6HvkuSE7TIBKq4lZZWhdLZfE8AWp4l3T)

</div>

Dhyan rahe ki ref array ka order **source array ke order se guaranteed match nahi karta**.

## Function Refs {#function-refs}

String key ke bajay, `ref` attribute ko ek function se bhi bind kiya ja sakta hai. Ye function har component update par call hota hai aur aapko ye pura control deta hai ki aap element reference ko kahan store karna chahte hain. Function ko element reference first argument ke roop me milta hai:

```vue-html
<input :ref="(el) => { /* assign el to a property or ref */ }">
```

Dhyan dein ki hum dynamic `:ref` binding ka use kar rahe hain taaki hum ek function pass kar sakein ref name string ke bajay. Jab element unmount hota hai, toh us function ka argument `null` hota hai. Aap chahein toh inline function ki jagah ek method ka bhi use kar sakte hain.
