# Single-File Components {#single-file-components}

## Introduction {#introduction}

Vue Single-File Components (yaani `*.vue` files, jise short me **SFC** bhi kehte hain) ek special file format hai jo hume allow karta hai ki hum ek hi file me template, logic **aur** styling ko encapsulate kar saken. Yaha ek example SFC diya gaya hai:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      greeting: 'Hello World!'
    }
  }
}
</script>

<template>
  <p class="greeting">{{ greeting }}</p>
</template>

<style>
.greeting {
  color: red;
  font-weight: bold;
}
</style>
```

</div>

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'
const greeting = ref('Hello World!')
</script>

<template>
  <p class="greeting">{{ greeting }}</p>
</template>

<style>
.greeting {
  color: red;
  font-weight: bold;
}
</style>
```

</div>

Jaise hum dekh sakte hain, Vue SFC ek natural extension hai classic HTML, CSS aur JavaScript trio ka. `<template>`, `<script>` aur `<style>` blocks ek hi file me component ka view, logic aur styling ko encapsulate aur colocate kar dete hain. Puri syntax [SFC Syntax Specification](/api/sfc-spec) me define hai.

## Why SFC {#why-sfc}

SFCs ko ek build step ki zaroorat hoti hai, lekin iske badle me bohot saare benefits milte hain:

- Familiar HTML, CSS aur JavaScript syntax use karke modularized components likhna
- [Colocation of inherently coupled concerns](#what-about-separation-of-concerns)
- Pre-compiled templates bina runtime compilation cost ke
- [Component-scoped CSS](/api/sfc-css-features)
- [Composition API ke sath aur ergonomic syntax](/api/sfc-script-setup)
- Template aur script ke cross-analysis se zyada compile-time optimizations
- [IDE support](/guide/scaling-up/tooling#ide-support) jisme auto-completion aur template expressions ke liye type-checking hoti hai
- Out-of-the-box Hot-Module Replacement (HMR) support

SFC Vue framework ka ek defining feature hai, aur recommended approach hai in scenarios me:

- Single-Page Applications (SPA)
- Static Site Generation (SSG)
- Koi bhi non-trivial frontend jaha ek build step justified ho better development experience (DX) ke liye

Saath hi, hum ye bhi samajhte hain ki kuch scenarios me SFC overkill lag sakte hain. Isi wajah se Vue abhi bhi plain JavaScript ke sath bina build step ke use kiya ja sakta hai. Agar aap sirf largely static HTML ko thodi interactions ke sath enhance karna chahte ho, to aap [petite-vue](https://github.com/vuejs/petite-vue) check kar sakte ho — ek 6 kB subset of Vue jo progressive enhancement ke liye optimize kiya gaya hai.

## How It Works {#how-it-works}

Vue SFC ek framework-specific file format hai jise pre-compile karna padta hai [@vue/compiler-sfc](https://github.com/vuejs/core/tree/main/packages/compiler-sfc) ke through standard JavaScript aur CSS me. Compiled SFC ek standard JavaScript (ES) module hota hai — iska matlab hai ki proper build setup ke sath aap SFC ko ek module ki tarah import kar sakte ho:

```js
import MyComponent from './MyComponent.vue'

export default {
  components: {
    MyComponent
  }
}
```

SFCs ke andar `<style>` tags development ke time pe normally native `<style>` tags ke roop me inject hote hain taki hot updates support ho sakein. Production me inhe extract karke ek single CSS file me merge kiya ja sakta hai.

Aap SFCs ke sath khel sakte ho aur dekh sakte ho ki ye kaise compile hote hain [Vue SFC Playground](https://play.vuejs.org/) me.

Real projects me, hum normally SFC compiler ko ek build tool ke sath integrate karte hain jaise [Vite](https://vitejs.dev/) ya [Vue CLI](http://cli.vuejs.org/) (jo [webpack](https://webpack.js.org/) par based hai). Vue official scaffolding tools provide karta hai taki aap SFCs ke sath jaldi se start kar sako. Aur details ke liye [SFC Tooling](/guide/scaling-up/tooling) section check karo.

## What About Separation of Concerns? {#what-about-separation-of-concerns}

Traditional web development background se aane wale kuch users soch sakte hain ki SFCs alag-alag concerns ko ek hi jagah mix kar rahe hain — jabki HTML/CSS/JS alag-alag hone chahiye the!

Is sawal ka jawab dene ke liye, pehle ye agree karna zaroori hai ki **separation of concerns ka matlab separation of file types nahi hota**. Engineering principles ka ultimate goal hota hai codebase ki maintainability improve karna. File types ke hisaab se alag kar dena, jabki frontend applications bohot complex hote ja rahe hain, us goal ko achieve karne me madad nahi karta.

Modern UI development me humne dekha hai ki codebase ko teen badi layers (HTML/CSS/JS) me divide karne ke bajay loosely-coupled components me todhna aur unhe compose karna zyada sensible hai. Ek component ke andar uska template, logic aur styles inherently coupled hote hain — aur unhe ek hi jagah rakhne se wo component zyada cohesive aur maintainable ban jata hai.

Note karo ki agar aapko Single-File Components ka idea pasand nahi bhi hai, tab bhi aap iske hot-reloading aur pre-compilation features ka faida utha sakte ho apna JavaScript aur CSS alag files me rakh kar [Src Imports](/api/sfc-spec#src-imports) use karke.
