---
outline: deep
---

# Server-Side Rendering (SSR) {#server-side-rendering-ssr}

## Overview {#overview}

### What is SSR? {#what-is-ssr}

Vue.js ek framework hai jo client-side applications banane ke liye use hota hai. By default, Vue components browser mein DOM create aur manipulate karte hain as output. Lekin, yeh bhi possible hai ki same components ko server pe HTML strings mein render karo, unhe directly browser ko bhejo, aur finally "hydrate" karke static markup ko client pe fully interactive app bana do.

Ek server-rendered Vue.js app ko "isomorphic" ya "universal" bhi bola ja sakta hai, kyunki aapke app ka majority code server **aur** client dono pe run hota hai.

### Why SSR? {#why-ssr}

Client-side Single-Page Application (SPA) ke comparison mein, SSR ka main advantage yeh hai:

- **Faster time-to-content**: yeh slow internet ya slow devices pe zyada noticeable hota hai. Server-rendered markup ko display hone ke liye saara JavaScript download aur execute hone ka wait nahi karna padta, toh user ko fully-rendered page jaldi dikh jata hai. Plus, initial visit pe data fetching server-side hoti hai, jo usually database se client se zyada fast connect hota hai. Isse [Core Web Vitals](https://web.dev/vitals/) metrics improve hote hain, user experience better hota hai, aur un apps ke liye critical ho sakta hai jahan time-to-content directly conversion rate se linked hai.

- **Unified mental model**: aapko poore app ke liye same language aur same declarative, component-oriented mental model milta hai, backend templating system aur frontend framework ke beech jump karne ki zarurat nahi.

- **Better SEO**: search engine crawlers ko directly fully rendered page milta hai.

  :::tip
  Abhi ke liye, Google aur Bing synchronous JavaScript applications ko easily index kar lete hain. Synchronous yahan key word hai. Agar aapka app loading spinner se start hota hai, phir content Ajax se fetch karta hai, toh crawler aapka wait nahi karega. Matlab agar aapke pages pe SEO important hai aur content asynchronously fetch hota hai, toh SSR zaruri ho sakta hai.
  :::

SSR use karte waqt kuch trade-offs bhi hain:

- Development constraints. Browser-specific code sirf kuch lifecycle hooks ke andar hi use kar sakte ho; kuch external libraries ko server-rendered app mein chalane ke liye special treatment chahiye hota hai.

- Build setup aur deployment requirements thode complex ho jate hain. Fully static SPA ko toh kisi bhi static file server pe deploy kar sakte ho, lekin server-rendered app ko aise environment chahiye jahan Node.js server run ho sake.

- Server-side load zyada hota hai. Node.js mein poora app render karna static files serve karne se zyada CPU-intensive hota hai, toh agar high traffic expect kar rahe ho toh server load ke liye ready raho aur caching strategies smartly use karo.

SSR use karne se pehle, sabse pehle yeh socho ki kya aapko waqai mein zarurat hai? Mostly yeh depend karta hai ki aapke app ke liye time-to-content kitna important hai. Jaise, agar aap ek internal dashboard bana rahe ho jahan initial load pe kuch hundred milliseconds ka farq matter nahi karta, toh SSR overkill ho jayega. Lekin, jahan time-to-content super critical hai, SSR best possible initial load performance de sakta hai.

### SSR vs. SSG {#ssr-vs-ssg}

**Static Site Generation (SSG)**, yaani pre-rendering, ek aur popular technique hai fast websites banane ke liye. Agar kisi page ko server-render karne ke liye jo data chahiye woh har user ke liye same hai, toh har request pe page render karne ki jagah, hum usse sirf ek baar, build process ke time pe render kar sakte hain. Pre-rendered pages static HTML files ki tarah generate aur serve hote hain.

SSG mein bhi SSR apps jaisi hi performance milti hai: time-to-content performance awesome hoti hai. Saath hi, SSR apps ke comparison mein SSG ko deploy karna sasta aur easy hai kyunki output sirf static HTML aur assets hote hain. Yahan keyword hai **static**: SSG sirf un pages pe apply hota hai jahan data static hai, yaani build time pe known hai aur requests ke beech change nahi hota. Jab bhi data change hota hai, naya deployment karna padta hai.

Agar aap sirf kuch marketing pages (jaise `/`, `/about`, `/contact`, etc.) ki SEO improve karne ke liye SSR dekh rahe ho, toh aapko SSR ki jagah SSG chahiye. SSG content-based websites jaise documentation sites ya blogs ke liye bhi best hai. Waise bhi, yeh website jo aap abhi padh rahe ho, woh bhi [VitePress](https://vitepress.dev/) se statically generate hoti hai, jo ek Vue-powered static site generator hai.

## Basic Tutorial {#basic-tutorial}

### Rendering an App {#rendering-an-app}

Chalo ek simple example dekhte hain jisme Vue SSR kaise kaam karta hai.

1. Naya directory banao aur `cd` karo usme
2. `npm init -y` run karo
3. `package.json` mein `"type": "module"` add karo taki Node.js [ES modules mode](https://nodejs.org/api/esm.html#modules-ecmascript-modules) mein chale.
4. `npm install vue` run karo
5. Ek `example.js` file banao:

```js
// this runs in Node.js on the server.
import { createSSRApp } from 'vue'
// Vue's server-rendering API is exposed under `vue/server-renderer`.
import { renderToString } from 'vue/server-renderer'

const app = createSSRApp({
  data: () => ({ count: 1 }),
  template: `<button @click="count++">{{ count }}</button>`
})

renderToString(app).then((html) => {
  console.log(html)
})
```

Phir run karo:

```sh
> node example.js
```

Command line pe yeh print hona chahiye:

```
<button>1</button>
```

[`renderToString()`](/api/ssr#rendertostring) ek Vue app instance leta hai aur ek Promise return karta hai jo app ka rendered HTML deta hai. Streaming rendering bhi possible hai [Node.js Stream API](https://nodejs.org/api/stream.html) ya [Web Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) se. Full details ke liye [SSR API Reference](/api/ssr) check karo.

Ab hum Vue SSR code ko ek server request handler mein shift kar sakte hain, jo application markup ko full page HTML ke andar wrap karta hai. Next steps ke liye hum [`express`](https://expressjs.com/) use karenge:

- `npm install express` run karo
- Yeh `server.js` file banao:

```js
import express from 'express'
import { createSSRApp } from 'vue'
import { renderToString } from 'vue/server-renderer'

const server = express()

server.get('/', (req, res) => {
  const app = createSSRApp({
    data: () => ({ count: 1 }),
    template: `<button @click="count++">{{ count }}</button>`
  })

  renderToString(app).then((html) => {
    res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        <title>Vue SSR Example</title>
      </head>
      <body>
        <div id="app">${html}</div>
      </body>
    </html>
    `)
  })
})

server.listen(3000, () => {
  console.log('ready')
})
```

Finally, `node server.js` run karo aur `http://localhost:3000` visit karo. Page pe button dikhna chahiye.

[Try it on StackBlitz](https://stackblitz.com/fork/vue-ssr-example-basic?file=index.js)

### Client Hydration {#client-hydration}

Agar aap button pe click karoge, toh number change nahi hoga. HTML client pe bilkul static hai kyunki humne browser mein Vue load nahi kiya.

Client-side app ko interactive banane ke liye, Vue ko **hydration** step perform karna padta hai. Hydration ke time, woh same Vue application create karta hai jo server pe run hui thi, har component ko uske DOM nodes se match karta hai, aur DOM event listeners attach karta hai.

App ko hydration mode mein mount karne ke liye, hume [`createSSRApp()`](/api/application#createssrapp) use karna hai `createApp()` ki jagah:

```js{2}
// this runs in the browser.
import { createSSRApp } from 'vue'

const app = createSSRApp({
  // ...same app as on server
})

// mounting an SSR app on the client assumes
// the HTML was pre-rendered and will perform
// hydration instead of mounting new DOM nodes.
app.mount('#app')
```

### Code Structure {#code-structure}

Notice karo ki hume same app implementation server aur client dono pe reuse karni padti hai. Yahin pe SSR app mein code structure pe dhyan dena padta hai - kaise same application code server aur client ke beech share karein?

Yahan hum ek simple setup dikhayenge. Pehle, app creation logic ko ek dedicated file `app.js` mein split karte hain:

```js
// app.js (shared between server and client)
import { createSSRApp } from 'vue'

export function createApp() {
  return createSSRApp({
    data: () => ({ count: 1 }),
    template: `<button @click="count++">{{ count }}</button>`
  })
}
```

Yeh file aur iski dependencies server aur client dono ke beech shared hoti hain - inhe **universal code** bolte hain. Universal code likhte waqt kuch cheezein dhyan mein rakhni padti hain, jaise ki hum [neeche discuss karenge](#writing-ssr-friendly-code).

Client entry universal code import karta hai, app create karta hai, aur mount karta hai:

```js [client.js]
import { createApp } from './app.js'

createApp().mount('#app')
```

Aur server bhi same app creation logic request handler mein use karta hai:

```js{2,5} [server.js]
// (irrelevant code omitted)
import { createApp } from './app.js'

server.get('/', (req, res) => {
  const app = createApp()
  renderToString(app).then(html => {
    // ...
  })
})
```

Iske alawa, client files ko browser mein load karne ke liye hume yeh bhi karna hoga:

1. `server.use(express.static('.'))` add karo `server.js` mein taki client files serve ho sakein.
2. HTML shell mein `<script type="module" src="/client.js"></script>` add karo taki client entry load ho sake.
3. Browser mein `import * from 'vue'` jaise usage ko support karne ke liye [Import Map](https://github.com/WICG/import-maps) HTML shell mein add karo.

[Try the completed example on StackBlitz](https://stackblitz.com/fork/vue-ssr-example?file=index.js). Ab button interactive hai!

## Higher Level Solutions {#higher-level-solutions}

Example se production-ready SSR app pe move karne ke liye aur bhi kaafi cheezein karni padengi. Hume yeh sab karna padega:

- Vue SFCs aur build step requirements ko support karo. Waise bhi, same app ke liye do builds coordinate karni padengi: ek client ke liye, ek server ke liye.

  :::tip
  Vue components SSR ke liye alag tarike se compile hote hain - templates ko string concatenations mein compile kiya jata hai, Virtual DOM render functions ki jagah, taki rendering performance efficient ho.
  :::

- Server request handler mein HTML ko sahi client-side asset links aur resource hints ke saath render karo. Ho sakta hai SSR aur SSG mode switch karna pade, ya dono mix karne ho ek hi app mein.

- Routing, data fetching, aur state management stores ko universal tarike se manage karo.

Ek complete implementation kaafi complex ho sakta hai aur aapke build toolchain pe depend karta hai. Isliye, hum recommend karte hain ki aap ek higher-level, opinionated solution use karo jo yeh complexity aapke liye handle kare. Neeche kuch recommended SSR solutions hain Vue ecosystem mein.

### Nuxt {#nuxt}

[Nuxt](https://nuxt.com/) ek higher-level framework hai jo Vue ecosystem pe bana hai aur universal Vue applications likhne ka streamlined experience deta hai. Aur bhi badiya, aap ise static site generator ke tarah bhi use kar sakte ho! Hum strongly recommend karte hain ki ek baar try karo.

### Quasar {#quasar}

[Quasar](https://quasar.dev) ek complete Vue-based solution hai jisse aap SPA, SSR, PWA, mobile app, desktop app, aur browser extension sab ek hi codebase se target kar sakte ho. Yeh sirf build setup hi nahi handle karta, balki Material Design compliant UI components ka full collection bhi deta hai.

### Vite SSR {#vite-ssr}

Vite mein [Vue server-side rendering](https://vitejs.dev/guide/ssr.html) ka built-in support hai, lekin yeh intentionally low-level hai. Agar aap directly Vite ke saath jaana chahte ho, toh [vite-plugin-ssr](https://vite-plugin-ssr.com/) check karo, jo community plugin hai aur kaafi challenging details aapke liye handle karta hai.

Aap ek example Vue + Vite SSR project bhi dekh sakte ho manual setup ke saath [yahan](https://github.com/vitejs/vite-plugin-vue/tree/main/playground/ssr-vue), jo base ke liye use ho sakta hai. Dhyan rahe, yeh tabhi recommend hai jab aap SSR / build tools mein experienced ho aur higher-level architecture pe full control chahte ho.

## Writing SSR-friendly Code {#writing-ssr-friendly-code}

Chahe aapka build setup ya higher-level framework kuch bhi ho, kuch principles sabhi Vue SSR apps pe apply hote hain.

### Reactivity on the Server {#reactivity-on-the-server}

SSR ke time, har request URL app ke desired state se map hota hai. Koi user interaction ya DOM updates nahi hote, toh server pe reactivity ki zarurat nahi hoti. By default, SSR ke time reactivity disabled hoti hai better performance ke liye.

### Component Lifecycle Hooks {#component-lifecycle-hooks}

Kyuki koi dynamic updates nahi hote, lifecycle hooks jaise <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span> ya <span class="options-api">`updated`</span><span class="composition-api">`onUpdated`</span> **SSR ke time call nahi hote** aur sirf client pe execute hote hain.<span class="options-api"> SSR ke time sirf `beforeCreate` aur `created` hooks call hote hain</span>

Aapko aisa code avoid karna chahiye jo side effects produce karta hai aur jisko cleanup ki zarurat hoti hai <span class="options-api">`beforeCreate` aur `created`</span><span class="composition-api">`setup()` ya `<script setup>` ke root scope mein</span>. Example: `setInterval` se timers setup karna. Client-side code mein hum timer setup karte hain aur <span class="options-api">`beforeUnmount`</span><span class="composition-api">`onBeforeUnmount`</span> ya <span class="options-api">`unmounted`</span><span class="composition-api">`onUnmounted`</span> mein teardown karte hain. Lekin, SSR ke time unmount hooks kabhi call nahi honge, toh timers hamesha chalenge. Isliye, side-effect code ko <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span> mein shift karo.

### Access to Platform-Specific APIs {#access-to-platform-specific-apis}

Universal code platform-specific APIs pe depend nahi kar sakta, toh agar aapka code browser-only globals jaise `window` ya `document` directly use karta hai, toh Node.js mein error aayega, aur vice-versa.

Aise tasks ke liye jo server aur client dono pe hain lekin platform APIs alag hain, recommended hai ki platform-specific implementations ko universal API ke andar wrap karo, ya aisi libraries use karo jo yeh kaam karti hain. Example: [`node-fetch`](https://github.com/node-fetch/node-fetch) use karke server aur client dono pe same fetch API use kar sakte ho.

Browser-only APIs ke liye, common approach hai ki unhe client-only lifecycle hooks jaise <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span> ke andar access karo.

Dhyan rahe, agar koi third-party library universal usage ke liye nahi bani hai, toh usse server-rendered app mein integrate karna tricky ho sakta hai. Shayad aap kuch globals mock karke chala lo, lekin yeh hacky hoga aur doosri libraries ke environment detection code ko interfere kar sakta hai.

### Cross-Request State Pollution {#cross-request-state-pollution}

State Management chapter mein humne [simple state management pattern Reactivity APIs ke saath](state-management#simple-state-management-with-reactivity-api) introduce kiya tha. SSR context mein, is pattern ko thoda adjust karna padta hai.

Pattern mein shared state JavaScript module ke root scope mein declare hoti hai. Isse woh **singletons** ban jati hain - yaani poore app ke lifecycle mein ek hi reactive object instance hota hai. Pure client-side Vue app mein yeh sahi kaam karta hai, kyunki browser mein har page visit pe modules fresh initialize hote hain.

Lekin SSR context mein, application modules usually sirf ek baar server boot hone pe initialize hote hain. Same module instances multiple server requests ke beech reuse hote hain, aur singleton state objects bhi. Agar hum shared singleton state ko user-specific data se mutate karte hain, toh woh accidentally doosre user ki request mein leak ho sakta hai. Isse **cross-request state pollution** bolte hain.

Technically, hum har request pe saare JavaScript modules re-initialize kar sakte hain, jaise browser mein hota hai. Lekin, modules initialize karna costly ho sakta hai, toh server performance pe impact padega.

Recommended solution hai ki har request pe poore application ka naya instance banao - router aur global stores ke saath. Phir, directly import karne ki jagah, shared state ko [app-level provide](/guide/components/provide-inject#app-level-provide) se provide karo aur components mein inject karo:

```js
// app.js (shared between server and client)
import { createSSRApp } from 'vue'
import { createStore } from './store.js'

// called on each request
export function createApp() {
  const app = createSSRApp(/* ... */)
  // create new instance of store per request
  const store = createStore(/* ... */)
  // provide store at the app level
  app.provide('store', store)
  // also expose store for hydration purposes
  return { app, store }
}
```

State Management libraries jaise Pinia isi cheez ko dhyan mein rakh ke bani hain. [Pinia's SSR guide](https://pinia.vuejs.org/ssr/) check karo for more details.

### Hydration Mismatch {#hydration-mismatch}

Agar pre-rendered HTML ka DOM structure client-side app ke expected output se match nahi karta, toh hydration mismatch error aayegi. Hydration mismatch mostly in reasons ki wajah se hoti hai:

1. Template mein invalid HTML nesting structure hai, aur rendered HTML browser ke native HTML parsing behavior se "correct" ho gaya. Example, ek common gotcha hai ki [`<div>` ko `<p>` ke andar nahi rakh sakte](https://stackoverflow.com/questions/8397852/why-cant-the-p-tag-contain-a-div-tag-inside-it):

   ```html
   <p><div>hi</div></p>
   ```

   Agar hum yeh server-rendered HTML mein produce karte hain, toh browser pehla `<p>` `<div>` aate hi terminate kar dega aur DOM structure kuch aisa banega:

   ```html
   <p></p>
   <div>hi</div>
   <p></p>
   ```

2. Render ke time data mein randomly generated values hain. Kyunki same application do baar chalegi - ek server pe, ek client pe - random values dono runs mein same hone ki guarantee nahi hai. Random-value-induced mismatches avoid karne ke do tareeke hain:

   1. `v-if` + `onMounted` use karo taki random values pe depend karne wala part sirf client pe render ho. Aapka framework bhi shayad built-in features deta ho, jaise VitePress ka `<ClientOnly>` component.

   2. Aisi random number generator library use karo jo seeds ke saath generate kar sake, aur ensure karo ki server aur client run same seed use kar rahe hain (jaise seed ko serialized state mein include karke client pe retrieve karo).

3. Server aur client alag time zones mein hain. Kabhi-kabhi hum timestamp ko user's local time mein convert karna chahte hain. Lekin, server run aur client run ke time zones hamesha same nahi hote, aur server run ke time user ka timezone reliably pata nahi hota. Aise cases mein, local time conversion bhi client-only operation hona chahiye.

Jab Vue ko hydration mismatch milti hai, toh woh automatically recover karne ki koshish karta hai aur pre-rendered DOM ko client-side state se match karta hai. Isse kuch rendering performance loss ho sakta hai kyunki galat nodes discard ho jayenge aur naye nodes mount honge, lekin mostly app expected tarike se kaam karta rahega. Fir bhi, development ke time hydration mismatches eliminate karna best hai.

#### Suppressing Hydration Mismatches <sup class="vt-badge" data-text="3.5+" /> {#suppressing-hydration-mismatches}

Vue 3.5+ mein, inevitable hydration mismatches ko selectively suppress kar sakte ho [`data-allow-mismatch`](/api/ssr#data-allow-mismatch) attribute use karke.

### Custom Directives {#custom-directives}

Zyada custom directives direct DOM manipulation karti hain, isliye SSR ke time ignore ho jati hain. Lekin, agar aap custom directive ko specify karna chahte ho ki server pe kaise render ho (jaise kaunse attributes rendered element pe add ho), toh `getSSRProps` directive hook use kar sakte ho:

```js
const myDirective = {
  mounted(el, binding) {
    // client-side implementation:
    // directly update the DOM
    el.id = binding.value
  },
  getSSRProps(binding) {
    // server-side implementation:
    // return the props to be rendered.
    // getSSRProps only receives the directive binding.
    return {
      id: binding.value
    }
  }
}
```

### Teleports {#teleports}

Teleports SSR ke time special handling maangte hain. Agar rendered app mein Teleports hain, toh teleported content rendered string ka part nahi hota. Easy solution yeh hai ki Teleport ko mount ke time conditionally render karo.

Agar aapko teleported content hydrate karna hai, toh woh ssr context object ke `teleports` property mein milta hai:

```js
const ctx = {}
const html = await renderToString(app, ctx)

console.log(ctx.teleports) // { '#teleported': 'teleported content' }
```

Aapko teleport markup ko final page HTML mein sahi jagah inject karna hoga, waise hi jaise main app markup inject karte ho.

:::tip
Teleports aur SSR ko saath use karte waqt `body` ko target karne se avoid karo - usually, `<body>` mein aur bhi server-rendered content hota hai jisse Teleports ke liye hydration ka correct starting location milna mushkil ho jata hai.

Iske bajaye, ek dedicated container use karo, jaise `<div id="teleported"></div>` jisme sirf teleported content ho.
:::
