---
footer: false
---

<script setup>
import { VTCodeGroup, VTCodeGroupTab } from '@vue/theme'
</script>

# Quick Start {#quick-start}

## Vue Online Try Karo {#try-vue-online}

- Vue ka ek quick demo dekhne ke liye, aap seedha hamare [Playground](https://play.vuejs.org/#eNo9jcEKwjAMhl/lt5fpQYfXUQfefAMvvRQbddC1pUuHUPrudg4HIcmXjyRZXEM4zYlEJ+T0iEPgXjn6BB8Zhp46WUZWDjCa9f6w9kAkTtH9CRinV4fmRtZ63H20Ztesqiylphqy3R5UYBqD1UyVAPk+9zkvV1CKbCv9poMLiTEfR2/IXpSoXomqZLtti/IFwVtA9A==) par try kar sakte ho.

- Agar aap ek simple HTML setup prefer karte ho bina kisi build step ke, toh aap [JSFiddle](https://jsfiddle.net/yyx990803/2ke1ab0z/) ka use kar sakte ho starting point ke roop mein.

- Agar aapko Node.js aur build tools ka concept already pata hai, toh aap browser ke andar hi ek complete build setup try kar sakte ho [StackBlitz](https://vite.new/vue) par.

- Recommended setup ka walkthrough dekhne ke liye, yeh interactive [Scrimba](http://scrimba.com/links/vue-quickstart) tutorial dekhiye jisme aapko Vue app ko run, edit aur deploy karna sikhaya gaya hai.

## Vue Application Banana {#creating-a-vue-application}

:::tip Prerequisites

- Command line ka basic use aana chahiye
- [Node.js](https://nodejs.org/) version 18.3 ya usse naya install hona chahiye

:::

Is section mein hum sikhenge ki apne local machine par Vue [Single Page Application](/guide/extras/ways-of-using-vue#single-page-application-spa) kaise create karte hain.  
Jo project create hoga, wo [Vite](https://vitejs.dev) par based build setup use karega  
aur humein Vue ke [Single-File Components](/guide/scaling-up/sfc) (SFCs) likhne ki suvidha dega.

Ensure karein ki aapne [Node.js](https://nodejs.org/) ka latest version install kiya hua hai,  
aur aapka current working directory wahi ho jahan aap project create karna chahte ho.  
Command line mein neeche diya gaya command run karein (without `$` sign):

::: code-group

```sh [npm]
$ npm create vue@latest
```

```sh [pnpm]
$ pnpm create vue@latest
```

```sh [yarn]
# For Yarn (v1+)
$ yarn create vue

# For Yarn Modern (v2+)
$ yarn create vue@latest

# For Yarn ^v4.11
$ yarn dlx create-vue@latest
```

```sh [bun]
$ bun create vue@latest
```

:::

Yeh command [create-vue](https://github.com/vuejs/create-vue) ko install aur execute karegi, jo Vue ka official project scaffolding tool hai. Iske baad aapke saamne kuch prompts aayenge jisme aapko optional features choose karne ka option milega — jaise ki TypeScript aur testing support.

<div class="language-sh"><pre><code><span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Project name: <span style="color:#888;">… <span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span></span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add TypeScript? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add JSX Support? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vue Router for Single Page Application development? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Pinia for state management? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vitest for Unit testing? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add an End-to-End Testing Solution? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Cypress / Nightwatch / Playwright</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add ESLint for code quality? <span style="color:#888;">… No / <span style="color:#89DDFF;text-decoration:underline">Yes</span></span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Prettier for code formatting? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vue DevTools 7 extension for debugging? (experimental) <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span></span>
<span style="color:#A6ACCD;">Scaffolding project in ./<span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span>...</span>
<span style="color:#A6ACCD;">Done.</span></code></pre></div>

Agar aap kisi option ko lekar sure nahi ho, toh filhaal ke liye bas `Enter` dabakar `No` choose kar lo. Jab project create ho jaye, toh neeche diye gaye steps follow karke dependencies install karo aur development server start karo:

::: code-group

```sh-vue [npm]
$ cd {{'<your-project-name>'}}
$ npm install
$ npm run dev
```

```sh-vue [pnpm]
$ cd {{'<your-project-name>'}}
$ pnpm install
$ pnpm run dev
```

```sh-vue [yarn]
$ cd {{'<your-project-name>'}}
$ yarn
$ yarn dev
```

```sh-vue [bun]
$ cd {{'<your-project-name>'}}
$ bun install
$ bun run dev
```

:::

Ab aapka pehla Vue project successfully chal raha hoga!  
Dhyaan dein ki jo example components generated project ke andar hain,  
wo [Composition API](/guide/introduction#composition-api) aur `<script setup>` ka use karke likhe gaye hain —  
na ki [Options API](/guide/introduction#options-api) ke saath.

Yahan kuch additional tips hain:

- Recommended IDE hai [Visual Studio Code](https://code.visualstudio.com/) + [Vue - Official extension](https://marketplace.visualstudio.com/items?itemName=Vue.volar).  
  Agar aap koi aur editor use karte ho toh [IDE support section](/guide/scaling-up/tooling#ide-support) check karo.

- Tooling aur backend framework ke integration ke details ke liye yeh section dekhein: [Tooling Guide](/guide/scaling-up/tooling)

- Build tool Vite ke baare mein aur jaankari ke liye: [Vite docs](https://vitejs.dev)

- Agar aap TypeScript use karna chahte ho, toh yeh guide dekhein: [TypeScript Usage Guide](typescript/overview)

Jab aap apne app ko production mein deploy karne ke liye ready ho jao,  
toh yeh command run karein:

::: code-group

```sh [npm]
$ npm run build
```

```sh [pnpm]
$ pnpm run build
```

```sh [yarn]
$ yarn build
```

```sh [bun]
$ bun run build
```

:::

Yeh command aapke app ka production-ready build banayegi,  
jo project ke `./dist` directory ke andar milega.  
Apne app ko production mein kaise deploy karein — iske liye [Production Deployment Guide](/guide/best-practices/production-deployment) zaroor padhein.

[Next Steps >](#next-steps)

## CDN ke zariye Vue ka use karna {#using-vue-from-cdn}

Aap Vue ko directly CDN ke through `<script>` tag se use kar sakte ho:

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

Yahan hum [unpkg](https://unpkg.com/) ka use kar rahe hain,  
lekin aap koi bhi aisa CDN use kar sakte ho jo npm packages serve karta ho —  
jaise [jsdelivr](https://www.jsdelivr.com/package/npm/vue) ya [cdnjs](https://cdnjs.com/libraries/vue).  
Aap chahein toh is file ko download karke khud bhi host kar sakte ho.

Jab aap Vue ko CDN ke zariye use karte ho, toh koi "build step" nahi hota.  
Is wajah se setup kaafi simple ho jaata hai,  
aur yeh method static HTML enhance karne ya backend framework ke saath integrate karne ke liye perfect hota hai.  
Lekin isme aap **Single-File Component (SFC)** syntax use nahi kar paayenge.

### Global Build ka use karna {#using-the-global-build}

Upar diya gaya link Vue ka _global build_ load karta hai,  
jisme saare top-level APIs global `Vue` object ke properties ke roop mein available hote hain.  
Neeche ek complete example diya gaya hai jo global build ka use karta hai:

<div class="options-api">

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp } = Vue

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/QWJwJLp)

</div>

<div class="composition-api">

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp, ref } = Vue

  createApp({
    setup() {
      const message = ref('Hello vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/eYQpQEG)

:::tip
Is guide mein Composition API ke kai examples `<script setup>` syntax ka use karte hain,  
jo ki build tools ki requirement rakhta hai.  
Agar aap Composition API ko bina build step ke use karna chahte ho,  
toh [`setup()` option](/api/composition-api-setup) ke usage ko dekhein.
:::

</div>

### ES Module Build ka use karna {#using-the-es-module-build}

Aage ki documentation mein hum mostly [ES modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) syntax ka use karenge.  
Aaj ke zyadatar modern browsers ES modules ko natively support karte hain,  
isliye hum Vue ko CDN ke zariye native ES modules ke through bhi use kar sakte hain, kuch is tarah:

<div class="options-api">

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

</div>

<div class="composition-api">

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    setup() {
      const message = ref('Hello Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

</div>

Dhyan dein ki hum yahan `<script type="module">` ka use kar rahe hain,  
aur jo CDN URL import ho raha hai, wo Vue ke **ES modules build** ko point karta hai.

<div class="options-api">

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/VwVYVZO)

</div>
<div class="composition-api">

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/MWzazEv)

</div>

### Import Maps enable karna {#enabling-import-maps}

Upar wale example mein humne full CDN URL se import kiya hai,  
lekin aage ki documentation mein aap aise code dekhoge:

```js
import { createApp } from 'vue'
```

Hum browser ko yeh bata sakte hain ki `vue` import ko kahan se load karna hai,  
aur yeh kaam hota hai [Import Maps](https://caniuse.com/import-maps) ke zariye:

<div class="options-api">

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'vue'

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/wvQKQyM)

</div>

<div class="composition-api">

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'vue'

  createApp({
    setup() {
      const message = ref('Hello Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/YzRyRYM)

</div>

Aap import map mein apne dusre dependencies ke entries bhi add kar sakte ho —  
lekin dhyan rahe ki unka path us library ke **ES modules version** ko hi point kare.

:::tip Import Maps Browser Support
Import Maps ek relatively naya browser feature hai.  
Ensure karein ki aap aisa browser use kar rahe ho jo is feature ko support karta ho:  
[Support range](https://caniuse.com/import-maps) check karein.  
Particularly, yeh Safari 16.4+ mein hi supported hai.
:::

:::warning Production Use ke Notes
Ab tak ke examples Vue ke development build par based the —  
agar aap Vue ko CDN ke through production mein use karna chahte ho,  
toh yeh zaruri hai ki aap [Production Deployment Guide](/guide/best-practices/production-deployment#without-build-tools) check karo.

Yeh zarur possible hai ki aap Vue bina kisi build system ke use karo,  
lekin ek alternate approach yeh ho sakta hai ki aap [`vuejs/petite-vue`](https://github.com/vuejs/petite-vue) ka use karo —  
jo un cases ke liye zyada suitable ho sakta hai jahan pehle [`jquery/jquery`](https://github.com/jquery/jquery) ya aaj kal [`alpinejs/alpine`](https://github.com/alpinejs/alpine) use hota tha.
:::

### Modules ko Split Karna {#splitting-up-the-modules}

Jaise-jaise hum guide mein aage badhenge, aapko apna code multiple JavaScript files mein split karna pad sakta hai taaki maintain karna easy ho jaye.  
For example:

```html [index.html]
<div id="app"></div>

<script type="module">
  import { createApp } from 'vue'
  import MyComponent from './my-component.js'

  createApp(MyComponent).mount('#app')
</script>
```

<div class="options-api">

```js [my-component.js]
export default {
  data() {
    return { count: 0 }
  },
  template: `<div>Count is: {{ count }}</div>`
}
```

</div>
<div class="composition-api">

```js [my-component.js]
import { ref } from 'vue'
export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `<div>Count is: {{ count }}</div>`
}
```

</div>

Agar aap upar diya gaya `index.html` file directly browser mein open karte ho,  
toh aapko ek error milega kyunki ES modules `file://` protocol ke saath kaam nahi karte —  
aur yahi protocol browser use karta hai jab aap koi local file open karte ho.

Security reasons ki wajah se, ES modules sirf `http://` protocol ke through hi kaam karte hain,  
jo browser normally tab use karta hai jab aap koi page web par open karte ho.  
Apne local system par ES modules ko chalane ke liye humein `index.html` ko `http://` protocol ke saath serve karna padega,  
ek local HTTP server ke zariye.

Ek local HTTP server start karne ke liye:

- Pehle ensure karein ki aapne [Node.js](https://nodejs.org/en/) install kiya hua hai
- Uske baad command line mein `npx serve` run karein — usi directory mein jahan aapka HTML file hai

Aap koi bhi doosra HTTP server use kar sakte ho  
jo static files ko sahi MIME types ke saath serve kar sake.

Aapne shayad notice kiya hoga ki imported component ka template JavaScript string ke andar inline likha gaya hai.  
Agar aap VS Code use kar rahe ho, toh aap [es6-string-html](https://marketplace.visualstudio.com/items?itemName=Tobermory.es6-string-html) extension install karke  
us string ko `/*html*/` comment ke saath prefix karo taaki aapko syntax highlighting mil sake.

## Aage ke Steps {#next-steps}

Agar aapne [Introduction](/guide/introduction) section skip kiya hai,  
toh strongly recommend kiya jaata hai ki usse zarur padhein uske baad hi aage ke documentation mein badhein.

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/guide/essentials/application.html">
    <p class="next-steps-link">Guide Continue Karo</p>
    <p class="next-steps-caption">Guide framework ke har part ko detail mein samjhata hai.</p>
  </a>
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">Tutorial Try Karo</p>
    <p class="next-steps-caption">Un logon ke liye jo hands-on tareeke se seekhna pasand karte hain.</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">Examples Dekho</p>
    <p class="next-steps-caption">Core features aur common UI tasks ke examples explore karo.</p>
  </a>
</div>
