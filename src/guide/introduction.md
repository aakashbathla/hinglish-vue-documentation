---
footer: false
---

# Introduction {#introduction}

:::info
Aap Vue 3 ki documentation padh rahe ho!

- Vue 2 ka support **31 Dec, 2023** ko khatam ho gaya. Aur jaankari ke liye dekho: [Vue 2 EOL](https://v2.vuejs.org/eol/)
- Agar aap Vue 2 se upgrade kar rahe ho, toh yeh guide zaroor padho: [Migration Guide](https://v3-migration.vuejs.org/)
  :::

<style src="@theme/styles/vue-mastery.css"></style>
<div class="vue-mastery-link">
  <a href="https://www.vuemastery.com/courses/" target="_blank">
    <div class="banner-wrapper">
      <img class="banner" alt="Vue Mastery banner" width="96px" height="56px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vuemastery-graphical-link-96x56.png" />
    </div>
    <p class="description">Vue seekhne ke liye video tutorials dekho <span>VueMastery.com</span> pe</p>
    <div class="logo-wrapper">
        <img alt="Vue Mastery Logo" width="25px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vue-mastery-logo.png" />
    </div>
  </a>
</div>

## What is Vue? {#what-is-vue}

Vue (pronounce hota hai /vjuː/, jaise **view**) ek JavaScript framework hai jo user interfaces banane ke kaam aata hai.  
Ye standard HTML, CSS aur JavaScript ke upar based hai aur aapko ek **declarative** aur **component-based** programming model deta hai, jisse aap simple se leke complex tak kisi bhi UI ko easily bana sakte ho.

Yahan ek chhota basic example diya gaya hai:

<div class="options-api">

```js
import { createApp } from 'vue'

createApp({
  data() {
    return {
      count: 0
    }
  }
}).mount('#app')
```

</div>
<div class="composition-api">

```js
import { createApp, ref } from 'vue'

createApp({
  setup() {
    return {
      count: ref(0)
    }
  }
}).mount('#app')
```

</div>

```vue-html
<div id="app">
  <button @click="count++">
    Count is: {{ count }}
  </button>
</div>
```

**Result**

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<div class="demo">
  <button @click="count++">
    Count is: {{ count }}
  </button>
</div>

Upar wale example mein Vue ke do core features dikh rahe hain:

- **Declarative Rendering**: Vue normal HTML ko extend karta hai ek aise template syntax ke saath jisme hum JavaScript state ke basis par HTML ka output clearly describe kar sakte hain.

- **Reactivity**: Vue automatically JavaScript ki state changes ko track karta hai aur jab bhi koi change hota hai, DOM (page ka content) smartly aur efficiently update ho jata hai.

Ho sakta hai aapke dimaag mein abhi kuch sawal aaye ho — lekin tension mat lo! Aage aapko documentation mein har ek chhoti-badi detail clearly samjhayi jaayegi. Abhi ke liye bas itna padho taaki aapko Vue ka ek high-level overview mil sake.

:::tip Prerequisites
Baaki documentation yeh maan kar chalti hai ki aapko HTML, CSS aur JavaScript ka basic knowledge hai.  
Agar aap bilkul naye ho frontend development mein, toh directly kisi framework se start karna thoda mushkil ho sakta hai — pehle basic samjho, fir Vue pe aao.

Apni knowledge test karne ke liye yeh links useful hain:

- [JavaScript basics](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)
- [HTML basics](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML)
- [CSS basics](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps)

Agar aapne pehle kisi aur framework ke saath kaam kiya hai, to wo helpful hoga — lekin zaroori nahi hai.
:::

## The Progressive Framework {#the-progressive-framework}

Vue ek aisa framework aur ecosystem hai jo frontend development mein lagbhag har common requirement ko cover karta hai.  
Lekin web duniya kaafi alag-alag tarah ka hota hai — hum web pe jo cheezein banate hain, unka form aur scale bahut vary karta hai.

Isiliye Vue ko design kiya gaya hai ekdum flexible aur dheere-dheere adopt hone waale framework ke roop mein.  
Aapke use-case ke hisaab se, Vue ko kai tariko se use kiya ja sakta hai:

- Bina build step ke simple HTML pages mein interactivity add karna
- Web Components ke form mein kisi bhi page mein use karna
- Single Page Application (SPA) banana
- Fullstack apps ya Server Side Rendering (SSR)
- Jamstack ya Static Site Generation (SSG)
- Desktop, mobile, WebGL ya even terminal apps ke liye use karna

Agar ye terms thodi confusing lag rahi hain, toh chinta mat karo!  
Tutorials aur guide sirf basic HTML aur JS knowledge expect karte hain — expert hona zaroori nahi hai.

Agar aap ek experienced developer ho aur Vue ko apne stack mein best way se integrate karna chahte ho,  
ya fir aap in sab concepts ka matlab samajhna chahte ho, toh yeh section dekho:  
👉 [Ways of Using Vue](/guide/extras/ways-of-using-vue)

Chahe aap Vue ko kis bhi level pe use karo, Vue ka core system har jagah common rehta hai.  
Agar aap beginner ho toh yeh knowledge aapke saath grow karega,  
aur agar aap experienced ho toh aap Vue ko efficiently optimize kar sakte ho — productivity compromise kiye bina.

**Isi liye Vue ko kehte hain “Progressive Framework”** —  
kyunki ye aapke skill aur project ke scale ke saath evolve ho sakta hai.

## Single-File Components {#single-file-components}

Zyadatar build-tool-enabled Vue projects mein hum apne components `.vue` files ke form mein likhte hain.  
Is tarah ke file format ko bola jaata hai **Single-File Component** ya short mein **SFC**.

Ek SFC file mein teen major parts hote hain:

- JavaScript (component ka logic)
- HTML (template)
- CSS (style/design)

Aur yeh sab ek hi file ke andar hote hain — neatly organized.

Yeh raha wahi pehle waala example, ab SFC format mein likha gaya hai:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>

SFC (Single-File Component) Vue ka ek aisa feature hai jo uski pehchaan banata hai,  
aur jab aapka use case build setup ki requirement rakhta ho, toh Vue components likhne ka recommended tareeka bhi yahi hai.  
Aap [SFC kaise aur kyun use hota hai](/guide/scaling-up/sfc) yeh detail mein dedicated section mein padh sakte ho —  
lekin filhaal itna jaan lo ki Vue aapke liye saare build tools ka setup khud handle karta hai.

## API Styles {#api-styles}

Vue components do alag-alag API styles mein likhe ja sakte hain: **Options API** aur **Composition API**.

### Options API {#options-api}

Options API mein hum component ka logic ek options object ke zariye define karte hain jaise ki `data`, `methods`, aur `mounted`.  
Jo properties in options ke through define hoti hain, unhe functions ke andar `this` ke zariye access kiya ja sakta hai,  
aur yeh `this` component instance ko point karta hai.

```vue
<script>
export default {
  // Properties returned from data() become reactive state
  // and will be exposed on `this`.
  data() {
    return {
      count: 0
    }
  },

  // Methods are functions that mutate state and trigger updates.
  // They can be bound as event handlers in templates.
  methods: {
    increment() {
      this.count++
    }
  },

  // Lifecycle hooks are called at different stages
  // of a component's lifecycle.
  // This function will be called when the component is mounted.
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNptkMFqxCAQhl9lkB522ZL0HNKlpa/Qo4e1ZpLIGhUdl5bgu9es2eSyIMio833zO7NP56pbRNawNkivHJ25wV9nPUGHvYiaYOYGoK7Bo5CkbgiBBOFy2AkSh2N5APmeojePCkDaaKiBt1KnZUuv3Ky0PppMsyYAjYJgigu0oEGYDsirYUAP0WULhqVrQhptF5qHQhnpcUJD+wyQaSpUd/Xp9NysVY/yT2qE0dprIS/vsds5Mg9mNVbaDofL94jZpUgJXUKBCvAy76ZUXY53CTd5tfX2k7kgnJzOCXIF0P5EImvgQ2olr++cbRE4O3+t6JxvXj0ptXVpye1tvbFY+ge/NJZt)

### Composition API {#composition-api}

Composition API ke saath, hum component ka logic imported API functions ka use karke define karte hain.  
SFCs (Single-File Components) mein Composition API aam taur par [`<script setup>`](/api/sfc-script-setup) ke saath use hoti hai.  
`setup` attribute ek signal hota hai jo Vue ko compile-time pe kuch special transformations karne ke liye kehata hai,  
jisse hum Composition API ko kam boilerplate ke saath use kar sakein.

Jaise ki, `<script setup>` ke andar declare kiye gaye imports aur top-level variables/functions template ke andar directly use kiye ja sakte hain.

Yahan wahi component diya gaya hai, bilkul same template ke saath,  
lekin ab Composition API aur `<script setup>` ka use karke likha gaya hai:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// reactive state
const count = ref(0)

// functions that mutate state and trigger updates
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNpNkMFqwzAQRH9lMYU4pNg9Bye09NxbjzrEVda2iLwS0spQjP69a+yYHnRYad7MaOfiw/tqSliciybqYDxDRE7+qsiM3gWGGQJ2r+DoyyVivEOGLrgRDkIdFCmqa1G0ms2EELllVKQdRQa9AHBZ+PLtuEm7RCKVd+ChZRjTQqwctHQHDqbvMUDyd7mKip4AGNIBRyQujzArgtW/mlqb8HRSlLcEazrUv9oiDM49xGGvXgp5uT5his5iZV1f3r4HFHvDprVbaxPhZf4XkKub/CDLaep1T7IhGRhHb6WoTADNT2KWpu/aGv24qGKvrIrr5+Z7hnneQnJu6hURvKl3ryL/ARrVkuI=)

### Kaunsa Choose Karein? {#which-to-choose}

Dono API styles common use cases ko handle karne mein fully capable hain.  
Yeh sirf do alag interfaces hain jo ek hi base system par kaam karte hain.  
Actually, Options API bhi Composition API ke upar hi banayi gayi hai!  
Vue ke fundamental concepts aur knowledge dono styles ke beech common hote hain.

Options API ka focus "component instance" ke concept par hota hai (`this` jaise ki example mein dekha).  
Yeh style un users ke liye zyada natural feel hoti hai jo OOP (Object-Oriented Programming) background se aate hain.  
Yeh beginners ke liye bhi friendly hoti hai, kyunki isme reactivity ka logic abstract hota hai aur code clearly options mein organized hota hai.

Composition API ka focus yeh hota hai ki aap reactive state variables ko function scope ke andar declare karo,  
aur complexity ko multiple functions ke through manage karo.  
Yeh zyada flexible hai, lekin isse achhe se use karne ke liye aapko Vue ki reactivity ka concept samajhna zaroori hota hai.  
Is flexibility ke wajah se aap powerful logic reuse aur organization patterns bana sakte ho.

Aap in dono styles ke comparison aur Composition API ke benefits ke baare mein aur padh sakte ho yahan:  
👉 [Composition API FAQ](/guide/extras/composition-api-faq)

Agar aap Vue mein naye ho, toh hamari general recommendation yeh hai:

- Learning ke liye jo style aapko easy lage, usse start karo.  
  Waise bhi, dono styles ke beech core concepts mostly shared hote hain.  
  Baad mein aap easily dusri style seekh sakte ho.

- Production ke liye:

  - Options API use karo agar aap build tools nahi use kar rahe ho,  
    ya Vue ko sirf low-complexity cases ke liye use karne wale ho (jaise progressive enhancement).

  - Composition API + Single-File Components use karo agar aap full Vue application banana chahte ho.

Learning phase ke dauraan aap kisi ek style mein fix hone ki zaroorat nahi hai.  
Documentation mein dono styles ke examples diye gaye hain jahan applicable ho,  
aur aap left sidebar ke top mein **API Preference switches** ke through style switch kar sakte ho.

## Abhi Bhi Questions Hain? {#still-got-questions}

Toh [FAQ](/about/faq) section zaroor check karo.

## Apna Learning Path Choose Karo {#pick-your-learning-path}

Har developer ka apna learning style hota hai.  
Aap apne comfort ke according learning path choose kar sakte ho —  
lekin agar possible ho, toh poora content padhna recommend kiya jata hai!

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">Tutorial Try Karo</p>
    <p class="next-steps-caption">Un logon ke liye jo hands-on tareeke se seekhna pasand karte hain.</p>
  </a>
  <a class="vt-box" href="/guide/quick-start.html">
    <p class="next-steps-link">Guide Padhkar Seekho</p>
    <p class="next-steps-caption">Guide aapko framework ke har pehlu se detail mein guzarti hai.</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">Examples Dekho</p>
    <p class="next-steps-caption">Core features aur common UI tasks ke examples explore karo.</p>
  </a>
</div>
