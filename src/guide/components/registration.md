# Component Registration {#component-registration}

> Ye page assume karta hai ki aap pehle hi [Components Basics](/guide/essentials/component-basics) padh chuke ho. Agar aap components ke liye naye ho, toh pehle wo padhein.

<VueSchoolLink href="https://vueschool.io/lessons/vue-3-global-vs-local-vue-components" title="Free Vue.js Component Registration Lesson"/>

Ek Vue component ko "register" karna padta hai taaki Vue ko pata chale ki jab template mein wo component mile, toh uski implementation kahan se leni hai. Components ko register karne ke do tareeke hote hain: global aur local.

## Global Registration {#global-registration}

Hum components ko globally available bana sakte hain current [Vue application](/guide/essentials/application) mein `.component()` method ka use karke:

```js
import { createApp } from 'vue'

const app = createApp({})

app.component(
  // the registered name
  'MyComponent',
  // the implementation
  {
    /* ... */
  }
)
```

Agar aap SFCs (Single File Components) use kar rahe ho, toh aapko import ki gayi `.vue` files ko register karna padega:

```js
import MyComponent from './App.vue'
```

.component() method ko chain bhi kiya ja sakta hai:

```js
app
  .component('ComponentA', ComponentA)
  .component('ComponentB', ComponentB)
  .component('ComponentC', ComponentC)
```

Globally registered components ko is application ke kisi bhi component ke template mein use kiya ja sakta hai:

```vue-html
<!-- yeh kisi bhi component ke andar kaam karega -->
<ComponentA/>
<ComponentB/>
<ComponentC/>
```

Yeh rule subcomponents par bhi apply hota hai, iska matlab yeh tino components _ek dusre ke andar bhi_ available honge.

## Local Registration {#local-registration}

Jabki global registration kaafi convenient hai, iske kuch drawbacks bhi hain:

1. Global registration ke wajah se build systems unused components ko remove nahi kar paate (isse "tree-shaking" kehte hain). Agar aapne koi component globally register kiya lekin usse kahin use nahi kiya, toh bhi woh final bundle mein include ho jayega.

2. Global registration se large applications mein dependencies ka structure less explicit ho jaata hai. Parent component ke liye yeh samajhna mushkil ho jaata hai ki child component kahan se aa raha hai. Yeh long-term maintainability ko impact karta hai, bilkul jaise bahut saare global variables ka use karna.

Local registration mein, component sirf us current component ke scope mein available hota hai. Isse dependency relationships zyada clear ho jaate hain, aur yeh tree-shaking ke liye bhi better hota hai.

<div class="composition-api">

Jab aap `<script setup>` ke saath SFC use karte ho, toh imported components ko bina explicitly register kiye directly use kiya ja sakta hai:

```vue
<script setup>
import ComponentA from './ComponentA.vue'
</script>

<template>
  <ComponentA />
</template>
```

Agar aap non-`<script setup>` use kar rahe ho, toh aapko `components` option ka use karna padega:

```js
import ComponentA from './ComponentA.js'

export default {
  components: {
    ComponentA
  },
  setup() {
    // ...
  }
}
```

</div>
<div class="options-api">

Local registration `components` option ke through hoti hai:

```vue
<script>
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
}
</script>

<template>
  <ComponentA />
</template>
```

</div>

`components` object ke andar jitni bhi properties hoti hain, unka key hota hai component ka registered name, aur value hoti hai component ka implementation. Upar diya gaya example ES2015 shorthand ka use karta hai, jo iske barabar hai:

```js
export default {
  components: {
    ComponentA: ComponentA
  }
  // ...
}
```

Yeh dhyan dena zaroori hai ki **locally registered components _descendant components_ mein available nahi hote**. Is example mein `ComponentA` sirf current component mein hi accessible hoga, uske kisi bhi child ya nested component mein nahi.

## Component Name Casing {#component-name-casing}

Is guide mein hum mostly PascalCase component names ka use kar rahe hain. Aisa karne ke kuch reasons hain:

1. PascalCase JavaScript identifiers ke liye valid hote hain. Isse JavaScript mein components ko import aur register karna easy ho jaata hai. IDEs bhi auto-complete better kar paate hain.

2. `<PascalCase />` template mein clearly dikhata hai ki yeh ek Vue component hai, na ki koi native HTML element. Isse Vue components aur custom web components mein differentiation easy ho jaata hai.

Yeh style tab recommend kiya jaata hai jab aap SFC ya string templates ka use kar rahe ho. Lekin jaise humne [in-DOM Template Parsing Caveats](/guide/essentials/component-basics#in-dom-template-parsing-caveats) mein discuss kiya, PascalCase tags in-DOM templates mein kaam nahi karte.

Good news yeh hai ki Vue kebab-case tags ko bhi PascalCase registered components ke saath resolve kar leta hai. Matlab agar aapne `MyComponent` naam ka component register kiya hai, toh aap usse Vue template mein `<MyComponent>` ya `<my-component>` dono tarah se use kar sakte ho. Isse aapko JavaScript registration code har template type ke liye alag-alag likhne ki zarurat nahi padti.
