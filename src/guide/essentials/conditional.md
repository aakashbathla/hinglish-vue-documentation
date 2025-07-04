# Conditional Rendering {#conditional-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/conditional-rendering-in-vue-3" title="Free Vue.js Conditional Rendering Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-conditionals-in-vue" title="Free Vue.js Conditional Rendering Lesson"/>
</div>

<script setup>
import { ref } from 'vue'
const awesome = ref(true)
</script>

## `v-if` {#v-if}

Directive `v-if` ka use kisi block ko conditionally render karne ke liye hota hai. Block sirf tab render hota hai jab expression truthy value return karta hai.

```vue-html
<h1 v-if="awesome">Vue is awesome!</h1>
```

## `v-else` {#v-else}

Aap `v-else` directive ka use `v-if` ke liye ek "else block" dikhane ke liye kar sakte ho:

```vue-html
<button @click="awesome = !awesome">Toggle</button>

<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

<div class="demo">
  <button @click="awesome = !awesome">Toggle</button>
  <h1 v-if="awesome">Vue is awesome!</h1>
  <h1 v-else>Oh no 😢</h1>
</div>

<div class="composition-api">

[Playground mein try karein](https://play.vuejs.org/#eNpFjkEOgjAQRa8ydIMulLA1hegJ3LnqBskAjdA27RQXhHu4M/GEHsEiKLv5mfdf/sBOxux7j+zAuCutNAQOyZtcKNkZbQkGsFjBCJXVHcQBjYUSqtTKERR3dLpDyCZmQ9bjViiezKKgCIGwM21BGBIAv3oireBYtrK8ZYKtgmg5BctJ13WLPJnhr0YQb1Lod7JaS4G8eATpfjMinjTphC8wtg7zcwNKw/v5eC1fnvwnsfEDwaha7w==)

</div>
<div class="options-api">

[Playground mein try karein](https://play.vuejs.org/#eNpFjj0OwjAMha9iMsEAFWuVVnACNqYsoXV/RJpEqVOQqt6DDYkTcgRSWoplWX7y56fXs6O1u84jixlvM1dbSoXGuzWOIMdCekXQCw2QS5LrzbQLckje6VEJglDyhq1pMAZyHidkGG9hhObRYh0EYWOVJAwKgF88kdFwyFSdXRPBZidIYDWvgqVkylIhjyb4ayOIV3votnXxfwrk2SPU7S/PikfVfsRnGFWL6akCbeD9fLzmK4+WSGz4AA5dYQY=)

</div>

A `v-else` element ko turant kisi `v-if` ya `v-else-if` element ke baad likhna zaroori hai — warna wo recognize nahi hoga.

## `v-else-if` {#v-else-if}

`v-else-if`, jaise ki naam se pata lagta hai, `v-if` ke liye ek "else if block" ke roop mein kaam karta hai. Isko multiple times chain bhi kiya ja sakta hai:

```vue-html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

`v-else` ki tarah, `v-else-if` element bhi turant kisi `v-if` ya `v-else-if` ke baad hona chahiye.

## `v-if` on `<template>` {#v-if-on-template}

Kyuki `v-if` ek directive hai, isey ek hi element pe lagana padta hai. Agar aap ek se zyada elements ko toggle karna chahte ho, toh aap `<template>` element ke upar `v-if` ka use kar sakte ho, jo ek invisible wrapper ka kaam karta hai. Final rendered output mein `<template>` element include nahi hota.

```vue-html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

`v-else` aur `v-else-if` ko bhi `<template>` ke saath use kiya ja sakta hai.

## `v-show` {#v-show}

Ek aur option kisi element ko conditionally dikhane ka hai `v-show` directive. Iska use lagbhag `v-if` jaisa hi hota hai:

```vue-html
<h1 v-show="ok">Hello!</h1>
```

Fark ye hai ki `v-show` wala element DOM mein hamesha rendered rahta hai; `v-show` sirf us element ki `display` CSS property ko toggle karta hai.

`v-show` `<template>` element ko support nahi karta, aur na hi `v-else` ke saath kaam karta hai.

## `v-if` vs. `v-show` {#v-if-vs-v-show}

`v-if` ek "real" conditional rendering hai kyunki ye ensure karta hai ki conditional block ke andar ke event listeners aur child components toggle ke dauraan properly destroy aur re-create ho jaayein.

`v-if` **lazy** bhi hota hai: agar condition initial render ke time false hoti hai, toh ye kuch nahi karta — conditional block tabhi render hota hai jab condition pehli baar true hoti hai.

Wahin doosri taraf, `v-show` bahut simple hota hai — element hamesha render hota hai chahe initial condition kuch bhi ho, aur sirf CSS ke through toggle hota hai.

Aam taur par, `v-if` ka toggle cost zyada hota hai jabki `v-show` ka initial render cost zyada hota hai. Isliye agar aapko kisi element ko baar-baar toggle karna hai, toh `v-show` use karo; aur agar condition runtime mein rarely change hoti hai, toh `v-if` use karo.

## `v-if` with `v-for` {#v-if-with-v-for}

Jab `v-if` aur `v-for` dono ek hi element par use hote hain, toh `v-if` pehle evaluate hota hai. Details ke liye dekhein: [list rendering guide](list#v-for-with-v-if)

::: warning Note
Yeh **recommend nahi** kiya jata ki `v-if` aur `v-for` ko ek hi element par use kiya jaaye kyunki inka precedence implicit hota hai. Detail ke liye dekhein [list rendering guide](list#v-for-with-v-if)
:::
