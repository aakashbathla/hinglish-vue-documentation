# Composables {#composables}

<script setup>
import { useMouse } from './mouse'
const { x, y } = useMouse()
</script>

:::tip
Ye section yeh assume karta hai ki aapko Composition API ka basic knowledge hai. Agar aap sirf Options API ke saath Vue seekh rahe ho, toh aap API Preference ko Composition API pe set kar sakte ho (left sidebar ke upar wale toggle ka use karke) aur dobara [Reactivity Fundamentals](/guide/essentials/reactivity-fundamentals) aur [Lifecycle Hooks](/guide/essentials/lifecycle) chapters padh sakte ho.
:::

## "Composable" kya hai? {#what-is-a-composable}

Vue applications ke context me, ek "composable" ek aisi function hoti hai jo Vue ki Composition API ka use karke **stateful logic** ko encapsulate (bandh karna) aur reuse karti hai.

Frontend applications banate waqt, hume aksar common tasks ke liye logic ko reuse karna padta hai. Jaise, hume dates ko format karna ho to hum uske liye ek reusable function bana lete hain. Ye formatter function **stateless logic** ko encapsulate karta hai: ye input leta hai aur turant expected output return karta hai. Stateless logic reuse karne ke liye bohot saari libraries available hain - jaise [lodash](https://lodash.com/) aur [date-fns](https://date-fns.org/), jo shayad aapne pehle suna hoga.

Iske contrast me, **stateful logic** me aisi state manage karni hoti hai jo time ke sath change hoti rahe. Ek simple example hai page par mouse ka current position track karna. Real-world scenarios me ye aur complex ho sakta hai jaise touch gestures ya database connection status track karna.

## Mouse Tracker Example {#mouse-tracker-example}

Agar hum mouse tracking functionality ko Composition API ka use karke directly ek component ke andar implement karein, toh wo kuch aisa dikhega:

```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const x = ref(0)
const y = ref(0)

function update(event) {
  x.value = event.pageX
  y.value = event.pageY
}

onMounted(() => window.addEventListener('mousemove', update))
onUnmounted(() => window.removeEventListener('mousemove', update))
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

But agar humein yehi logic multiple components me reuse karna ho, toh hum is logic ko ek external file me extract kar sakte hain, ek composable function ke roop me:

```js
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

// by convention, composable function names start with "use"
export function useMouse() {
  // state encapsulated and managed by the composable
  const x = ref(0)
  const y = ref(0)

  // a composable can update its managed state over time.
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // a composable can also hook into its owner component's
  // lifecycle to setup and teardown side effects.
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // expose managed state as return value
  return { x, y }
}
```

Aur is tarah se ise components me use kiya ja sakta hai:

```vue
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

<div class="demo">
  Mouse position is at: {{ x }}, {{ y }}
</div>

[Try it in the Playground](https://play.vuejs.org/#eNqNkj1rwzAQhv/KocUOGKVzSAIdurVjoQUvJj4XlfgkJNmxMfrvPcmJkkKHLrbu69H7SlrEszFyHFDsxN6drDIeHPrBHGtSvdHWwwKDwzfNHwjQWd1DIbd9jOW3K2qq6aTJxb6pgpl7Dnmg3NS0365YBnLgsTfnxiNHACvUaKe80gTKQeN3sDAIQqjignEhIvKYqMRta1acFVrsKtDEQPLYxuU7cV8Msmg2mdTilIa6gU5p27tYWKKq1c3ENphaPrGFW25+yMXsHWFaFlfiiOSvFIBJjs15QJ5JeWmaL/xYS/Mfpc9YYrPxl52ULOpwhIuiVl9k07Yvsf9VOY+EtizSWfR6xKK6itgkvQ/+fyNs6v4XJXIsPwVL+WprCiL8AEUxw5s=)

Jaise hum dekh sakte hain, core logic same hi rehta hai - bas hume usse ek external function me move karna padta hai aur woh state return karni hoti hai jo expose honi chahiye. Bilkul component ke andar ki tarah, aap [Composition API functions](/api/#composition-api) ka pura use composables me kar sakte ho. Ab wahi `useMouse()` functionality kisi bhi component me use ki ja sakti hai.

Composables ki sabse badi baat ye hai ki aap unhe nest bhi kar sakte ho: ek composable function ek ya zyada dusre composable functions ko call kar sakta hai. Isse hum choti-choti, isolated units ka use karke complex logic compose kar sakte hain — bilkul waise hi jaise hum poore application ko components ka use karke compose karte hain. Isi wajah se is pattern ko possible banane wale APIs ke collection ko **Composition API** kaha gaya.

For example, hum DOM event listener ko add aur remove karne ka logic ek alag composable me extract kar sakte hain:

```js
// event.js
import { onMounted, onUnmounted } from 'vue'

export function useEventListener(target, event, callback) {
  // if you want, you can also make this
  // support selector strings as target
  onMounted(() => target.addEventListener(event, callback))
  onUnmounted(() => target.removeEventListener(event, callback))
}
```

Aur ab hamara `useMouse()` composable simplify karke aise ban sakta hai:

```js{3,9-12}
// mouse.js
import { ref } from 'vue'
import { useEventListener } from './event'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  useEventListener(window, 'mousemove', (event) => {
    x.value = event.pageX
    y.value = event.pageY
  })

  return { x, y }
}
```

:::tip
Har ek component instance jo `useMouse()` call karega, uske liye `x` aur `y` state ki apni copy banegi. Matlab ek component ka state doosre ke sath interfere nahi karega.  
Agar aapko components ke beech shared state manage karni hai, toh [State Management](/guide/scaling-up/state-management) chapter padho.
:::

## Async State Example {#async-state-example}

`useMouse()` composable koi arguments nahi leta, isliye chalo ek aur example dekhte hain jo arguments use karta ho. Jab hum async data fetching karte hain, toh humein aksar alag-alag states handle karni padti hain: loading, success, aur error.

```vue
<script setup>
import { ref } from 'vue'

const data = ref(null)
const error = ref(null)

fetch('...')
  .then((res) => res.json())
  .then((json) => (data.value = json))
  .catch((err) => (error.value = err))
</script>

<template>
  <div v-if="error">Oops! Error encountered: {{ error.message }}</div>
  <div v-else-if="data">
    Data loaded:
    <pre>{{ data }}</pre>
  </div>
  <div v-else>Loading...</div>
</template>
```

Har component mein jise data fetch karna ho, yeh pattern baar‑baar repeat karna tedious hoga. Chalo isse ek composable mein nikaal lete hain:

```js
// fetch.js
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  fetch(url)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))

  return { data, error }
}
```

Ab apne component me hum bas aise kar sakte hain:

```vue
<script setup>
import { useFetch } from './fetch.js'

const { data, error } = useFetch('...')
</script>
```

### Accepting Reactive State {#accepting-reactive-state}

`useFetch()` ek static URL string leta hai input ke roop me — isliye ye sirf ek hi baar fetch karta hai aur phir kaam khatam. Agar hume chahiye ki URL change hote hi ye dubara fetch kare, to hume reactive state ko composable function me pass karna padega, aur composable ko aise watchers banane chahiye jo passed state ko dekhkar action perform karein.

For example, `useFetch()` ko ek ref accept karna chahiye:

```js
const url = ref('/initial-url')

const { data, error } = useFetch(url)

// this should trigger a re-fetch
url.value = '/new-url'
```

Or, accept a [getter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description):

```js
// re-fetch when props.id changes
const { data, error } = useFetch(() => `/posts/${props.id}`)
```

Hum apne existing implementation ko [`watchEffect()`](/api/reactivity-core.html#watcheffect) aur [`toValue()`](/api/reactivity-utilities.html#tovalue) APIs ke saath refactor kar sakte hain:

```js{8,13}
// fetch.js
import { ref, watchEffect, toValue } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  const fetchData = () => {
    // reset state before fetching..
    data.value = null
    error.value = null

    fetch(toValue(url))
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))
  }

  watchEffect(() => {
    fetchData()
  })

  return { data, error }
}
```

`toValue()` ek API hai jo Vue 3.3 me add hui thi. Ye refs ya getters ko normalize karke unki values return karti hai. Agar argument ek ref hai, toh ye ref ki value return karega; agar argument ek function hai, toh function ko call karke uska return value dega. Aur agar koi simple value pass ki gayi hai, toh usko as-is return kar dega. Ye [`unref()`](/api/reactivity-utilities.html#unref) jaisa hi kaam karta hai, bas functions ke liye special treatment ke sath.

Dhyaan do ki `toValue(url)` **andar** `watchEffect` callback ke call hota hai. Isse ensure hota hai ki koi bhi reactive dependency jo `toValue()` ke andar access hoti hai, wo watcher ke through track ho jaye.

Is naye version me `useFetch()` ab static URL strings, refs, aur getters sab accept kar sakta hai, jo ise zyada flexible banata hai. Watch effect turant run hoga, aur `toValue(url)` me jitni dependencies track hongi unko observe karega. Agar koi dependency track nahi hoti (jaise url already ek string hai), toh effect sirf ek baar chalega; aur agar dependency reactive hai, toh jab bhi wo change hogi, effect dobara run hoga.

Here's [the updated version of `useFetch()`](https://play.vuejs.org/#eNp9Vdtu20YQ/ZUpUUA0qpAOjL4YktCbC7Rom8BN8sSHrMihtfZql9iLZEHgv2dml6SpxMiDIWkuZ+acmR2fs1+7rjgEzG6zlaut7Dw49KHbVFruO2M9nMFiu4Ta7LvgsYEeWmv2sKCkxSwoOPwTfb2b/EU5mopHR5GVro12HrbC4UerYA2Lnfeduy3LR2d0p0SNO6MatIU/dbI2DRZUtPSmMa4kgJQuG8qkjvLF28XVaAwRb2wxz69gvZkK/UQ5xUGogBQ/ZpyhEV4sAa01lnpeTwRyApsFWvT2RO6Eea40THBMgfq6NLwlS1/pVZnUJB3ph8c98fNIvwD+MaKBzkQut2xYbYP3RsPhTWvsusokSA0/Vxn8UitZP7GFSX/+8Sz7z1W2OZ9BQt+vypQXS1R+1cgDQciW4iMrimR0wu8270znfoC7SBaJWdAeLTa3QFgxuNijc+IBIy5PPyYOjU19RDEI954/Z/UptKTy6VvqA5XD1AwLTTl/0Aco4s5lV51F5sG+VJJ+v4qxYbmkfiiKYvSvyknPbJnNtoyW+HJpj4Icd22LtV+CN5/ikC4XuNL4HFPaoGsvie3FIqSJp1WIzabl00HxkoyetEVfufhv1kAu3EnX8z0CKEtKofcGzhMb2CItAELL1SPlFMV1pwVj+GROc/vWPoc26oDgdxhfSArlLnbWaBOcOoEzIP3CgbeifqLXLRyICaDBDnVD+3KC7emCSyQ4sifspOx61Hh4Qy/d8BsaOEdkYb1sZS2FoiJKnIC6FbqhsaTVZfk8gDgK6cHLPZowFGUzAQTNWl/BUSrFbzRYHXmSdeAp28RMsI0fyFDaUJg9Spd0SbERZcvZDBRleCPdQMCPh8ARwdRRnBCTjGz5WkT0i0GlSMqixTR6VKyHmmWEHIfV+naSOETyRx8vEYwMv7pa8dJU+hU9Kz2t86ReqjcgaTzCe3oGpEOeD4uyJOcjTXe+obScHwaAi82lo9dC/q/wuyINjrwbuC5uZrS4WAQeyTN9ftOXIVwy537iecoX92kR4q/F1UvqIMsSbq6vo5XF6ekCeEcTauVDFJpuQESvMv53IBXadx3r4KqMrt0w0kwoZY5/R5u3AZejvd5h/fSK/dE9s63K3vN7tQesssnnhX1An9x3//+Hz/R9cu5NExRFf8d5zyIF7jGF/RZ0Q23P4mK3f8XLRmfhg7t79qjdSIobjXLE+Cqju/b7d6i/tHtT3MQ8VrH/Ahstp5A=), with an artificial delay and randomized error for demo purposes.

## Conventions and Best Practices {#conventions-and-best-practices}

### Naming {#naming}

Ye ek convention hai ki composable functions ka naam camelCase me ho aur "use" se start ho.

### Input Arguments {#input-arguments}

Ek composable ref ya getter arguments ko accept kar sakta hai, chahe wo unpe reactivity ke liye depend na kare. Agar aap ek composable likh rahe ho jo dusre developers bhi use karenge, toh yeh accha idea hai ki input arguments ke case me refs ya getters ko bhi handle karo instead of sirf raw values. Iske liye [`toValue()`](/api/reactivity-utilities#tovalue) utility function kaafi useful hai.

```js
import { toValue } from 'vue'

function useFeature(maybeRefOrGetter) {
  // If maybeRefOrGetter is a ref or a getter,
  // its normalized value will be returned.
  // Otherwise, it is returned as-is.
  const value = toValue(maybeRefOrGetter)
}
```

If your composable reactive effects create karta hai jab input ek ref ya getter ho, toh ensure karo ki ya toh explicitly `watch()` ke through us ref / getter ko observe karo, ya phir `toValue()` ko `watchEffect()` ke andar call karo taki wo properly track ho sake.

Jo [useFetch() implementation pehle discuss ki gayi thi](#accepting-reactive-state), wo ek clear example hai ek composable ka jo refs, getters aur plain values tino ko input argument ke roop me accept karta hai.

### Return Values {#return-values}

Aapne notice kiya hoga ki hum composables me hamesha `ref()` use kar rahe the instead of `reactive()`. Recommended convention yehi hai ki composables hamesha ek plain, non-reactive object return karein jisme multiple refs ho. Isse wo components me destructure kiye jaa sakte hain aur phir bhi reactivity retain hoti hai:

```js
// x aur y refs hain
const { x, y } = useMouse()
```

Agar ek composable se reactive object return kiya gaya, toh destructure karte waqt wo reactivity connection lose kar dega jo composable ke andar ke state se hota hai. Lekin agar refs return hote hain toh wo connection retain rahega.

Agar aap prefer karte ho ki composables se returned state ko object properties ke tarah use karna, toh aap returned object ko reactive() me wrap kar sakte ho taki refs unwrap ho jayein. Example:

```js
const mouse = reactive(useMouse())
// mouse.x is linked to original ref
console.log(mouse.x)
```

```vue-html
Mouse position is at: {{ mouse.x }}, {{ mouse.y }}
```

### Side Effects {#side-effects}

Composables me side effects (jaise DOM event listeners add karna ya data fetch karna) karna theek hai, lekin in rules ka dhyaan rakho:

- Agar aap ek application pe kaam kar rahe ho jo [Server-Side Rendering](/guide/scaling-up/ssr) (SSR) use karta hai, toh DOM-specific side effects hamesha post-mount lifecycle hooks (jaise `onMounted()`) me karo. Ye hooks sirf browser me call hote hain, toh aapko surety hoti hai ki unke andar ka code DOM ko access kar sakta hai.

- Side effects ko hamesha `onUnmounted()` me clean up karo. Example ke liye, agar ek composable ek DOM event listener set karta hai, toh usse `onUnmounted()` me remove bhi karna chahiye (jaise humne `useMouse()` example me dekha). Ek acha idea ye hai ki aap aisa composable banao jo automatically clean-up kare, jaise `useEventListener()` example.

### Usage Restrictions {#usage-restrictions}

Composables ko sirf `<script setup>` ya `setup()` hook ke andar hi call karna chahiye. Aur unhe in contexts me **synchronously** call karna chahiye. Kuchh cases me, aap unhe lifecycle hooks jaise `onMounted()` me bhi call kar sakte ho.

Ye restrictions important hain kyunki sirf inhi contexts me Vue current active component instance ko determine kar paata hai. Active component instance access karna zaroori hai taki:

1. Lifecycle hooks usme register ho sakein.
2. Computed properties aur watchers usse linked ho sakein, taki jab instance unmount ho toh wo dispose ho jayein aur memory leaks na ho.

:::tip
`<script setup>` ek hi aisi jagah hai jahan aap composables ko `await` ke baad bhi call kar sakte ho. Compiler async operation ke baad automatically active instance context ko restore kar deta hai.
:::

## Extracting Composables for Code Organization {#extracting-composables-for-code-organization}

Composables ko sirf reuse ke liye hi nahi, balki code organization ke liye bhi extract kiya jaa sakta hai. Jaise-jaise aapke components ka complexity badhta hai, aapke components itne bade ho sakte hain ki unhe samajhna ya navigate karna mushkil ho jaye. Composition API aapko ye flexibility deta hai ki aap apne component code ko logical concerns ke basis par chhoti-chhoti functions me tod kar organize kar sako.

```vue
<script setup>
import { useFeatureA } from './featureA.js'
import { useFeatureB } from './featureB.js'
import { useFeatureC } from './featureC.js'

const { foo, bar } = useFeatureA()
const { baz } = useFeatureB(foo)
const { qux } = useFeatureC(baz)
</script>
```

To some extent, aap in extracted composables ko component-scoped services ki tarah soch sakte ho jo ek doosre se baat kar sakte hain.

## Using Composables in Options API {#using-composables-in-options-api}

Agar aap Options API use kar rahe ho, toh composables ko `setup()` ke andar call karna padega, aur jo bindings return hote hain unhe `setup()` se return karna zaroori hai taki wo `this` aur template me expose ho saken:

```js
import { useMouse } from './mouse.js'
import { useFetch } from './fetch.js'

export default {
  setup() {
    const { x, y } = useMouse()
    const { data, error } = useFetch('...')
    return { x, y, data, error }
  },
  mounted() {
    // setup() exposed properties can be accessed on `this`
    console.log(this.x)
  }
  // ...other options
}
```

## Comparisons with Other Techniques {#comparisons-with-other-techniques}

### vs. Mixins {#vs-mixins}

Vue 2 se aane wale users [mixins](/api/options-composition#mixins) option se familiar honge, jisme component logic ko reusable units me extract kar sakte the. Lekin mixins ke kuch drawbacks hain:

1. **Properties ka unclear source**: Agar bahut saare mixins use ho rahe hain, toh samajhna mushkil ho jata hai ki kaunsa property kis mixin se aa raha hai. Is wajah se composables ke liye refs + destructure pattern recommend kiya jata hai, taki property ka source clear ho jaye.

2. **Namespace collisions**: Alag authors ke multiple mixins ek hi property key register kar sakte hain, jisse collision ho jata hai. Composables me aap destructured variables ka naam change karke ise avoid kar sakte ho.

3. **Implicit cross-mixin communication**: Agar multiple mixins ek doosre ke saath interact karna chahte hain, toh unhe shared property keys par rely karna padta hai, jo unhe tightly coupled bana deta hai. Composables me aap ek composable ka return value doosre me as argument pass kar sakte ho, bilkul normal functions ki tarah.

Inhi reasons ki wajah se Vue 3 me mixins use karna recommend nahi hai. Ye feature sirf migration aur familiarity ke liye rakha gaya hai.

### vs. Renderless Components {#vs-renderless-components}

Component slots chapter me humne [Renderless Component](/guide/components/slots#renderless-components) pattern discuss kiya tha (scoped slots ke basis par). Humne wahi mouse tracking demo renderless components ke sath bhi implement kiya tha.

Composables ka main advantage ye hai ki ye extra component instance overhead create nahi karte. Agar application me renderless components ka zyada use ho, toh unki extra component instances performance ko impact kar sakti hain.

Recommendation ye hai:

- Pure logic reuse karne ke liye **composables** use karo.
- Logic + visual layout dono reuse karne ke liye **components** use karo.

### vs. React Hooks {#vs-react-hooks}

Agar aap React se familiar ho, toh aap notice karenge ki Vue composables React ke custom hooks ki tarah lagte hain. Composition API partly React hooks se inspired hai. Dono ka purpose logic composition simplify karna hai.

Lekin ek major difference hai: Vue composables Vue ke fine-grained reactivity system par based hain, jo React hooks ke execution model se fundamentally different hai. Is par aur detail me [Composition API FAQ](/guide/extras/composition-api-faq#comparison-with-react-hooks) chapter me discussion hai.

## Further Reading {#further-reading}

- [Reactivity In Depth](/guide/extras/reactivity-in-depth): Vue ke reactivity system ka low-level understanding ke liye.
- [State Management](/guide/scaling-up/state-management): Multiple components ke beech state share karne ke patterns ke liye.
- [Testing Composables](/guide/scaling-up/testing#testing-composables): Composables ke unit testing tips.
- [VueUse](https://vueuse.org/): Vue composables ka ek bada collection. Source code khud bhi ek accha learning resource hai.
