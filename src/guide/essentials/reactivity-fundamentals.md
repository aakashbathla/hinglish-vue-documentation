---
outline: deep
---

# Reactivity Fundamentals {#reactivity-fundamentals}

:::tip API Preference
Is page mein aur guide ke kai aane wale chapters mein Options API aur Composition API ke liye alag content diya gaya hai. Aapki current preference hai <span class="options-api">Options API</span><span class="composition-api">Composition API</span>. Aap left sidebar ke top par "API Preference" switches ka use karke API styles ke beech toggle kar sakte ho.
:::

<div class="options-api">

## Reactive State Declare Karna \* {#declaring-reactive-state}

Options API mein hum component ka reactive state declare karne ke liye `data` option ka use karte hain. Is option ka value ek function hona chahiye jo ek object return karta hai. Vue is function ko tab call karta hai jab ek naya component instance create hota hai, aur return kiya gaya object Vue ke reactivity system ke andar wrap ho jaata hai. Is object ke jitne bhi top-level properties hote hain, unhe component instance par proxy kiya jaata hai (yaani methods aur lifecycle hooks mein `this` ke through access kiya ja sakta hai):

```js{2-6}
export default {
  data() {
    return {
      count: 1
    }
  },

  // `mounted` is a lifecycle hook which we will explain later
  mounted() {
    // `this` refers to the component instance.
    console.log(this.count) // => 1

    // data can be mutated as well
    this.count = 2
  }
}
```

[Try it in the Playground](https://play.vuejs.org/#eNpFUNFqhDAQ/JXBpzsoHu2j3B2U/oYPpnGtoetGkrW2iP/eRFsPApthd2Zndilex7H8mqioimu0wY16r4W+Rx8ULXVmYsVSC9AaNafz/gcC6RTkHwHWT6IVnne85rI+1ZLr5YJmyG1qG7gIA3Yd2R/LhN77T8y9sz1mwuyYkXazcQI2SiHz/7iP3VlQexeb5KKjEKEe2lPyMIxeSBROohqxVO4E6yV6ppL9xykTy83tOQvd7tnzoZtDwhrBO2GYNFloYWLyxrzPPOi44WWLWUt618txvASUhhRCKSHgbZt2scKy7HfCujGOqWL9BVfOgyI=)

Ye instance properties sirf tabhi add hoti hain jab component instance first time create hota hai,  
isliye aapko ensure karna chahiye ki `data` function se return hone wale object mein saari required properties pehle se defined hoon.  
Jahaan zarurat ho, wahan `null`, `undefined` ya koi placeholder value use karein un properties ke liye jinki value abhi available nahi hai.

Aap chahein to `this` par directly koi nayi property add kar sakte ho bina `data` mein include kiye.  
Lekin is tareeke se add ki gayi properties reactive updates trigger nahi karengi.

Vue apne built-in APIs ko component instance ke through expose karte waqt `$` prefix ka use karta hai.  
Saath hi, `_` prefix internal properties ke liye reserve hai.  
Isliye top-level `data` properties ke naam start hote hue `$` ya `_` se avoid karne chahiye.

### Reactive Proxy vs. Original \* {#reactive-proxy-vs-original}

Vue 3 mein data ko reactive banane ke liye [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) ka use hota hai.  
Jo log Vue 2 se aa rahe hain, unhe yeh edge case samajhna zaroori hai:

```js
export default {
  data() {
    return {
      someObject: {}
    }
  },
  mounted() {
    const newObject = {}
    this.someObject = newObject

    console.log(newObject === this.someObject) // false
  }
}
```

Jab aap `this.someObject` ko access karte ho uske assign hone ke baad,  
toh aapko uska value ek reactive proxy ke roop mein milega — jo ki original `newObject` ka proxy hota hai.  
**Vue 2 ke unlike, original `newObject` ko reactive nahi banaya jaata:  
isliye hamesha reactive state ko `this` ke property ke roop mein hi access karo.**

</div>

<div class="composition-api">

## Reactive State Declare Karna \*\* {#declaring-reactive-state-1}

### `ref()` \*\* {#ref}

Composition API mein reactive state declare karne ka recommended tareeka hai [`ref()`](/api/reactivity-core#ref) function ka use:

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()` ek argument leta hai aur usse ek ref object ke andar wrap karke return karta hai,  
jisme ek `.value` property hoti hai:

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

> See also: [Typing Refs](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

Template ke andar refs ko access karne ke liye, unhe component ke `setup()` function ke andar declare karke return karna padta hai:

```js{5,9-11}
import { ref } from 'vue'

export default {
  // `setup` is a special hook dedicated for the Composition API.
  setup() {
    const count = ref(0)

    // expose the ref to the template
    return {
      count
    }
  }
}
```

```vue-html
<div>{{ count }}</div>
```

Dhyan dein ki jab hum template mein ref use kar rahe the,  
toh humein `.value` lagane ki zarurat **nahi** padi.  
Template ke andar use hone par, convenience ke liye refs ko automatically unwrap kar diya jaata hai  
(kuch [exceptions](#caveat-when-unwrapping-in-templates) ke saath).

Aap event handlers ke andar ref ko directly mutate bhi kar sakte ho:

```vue-html{1}
<button @click="count++">
  {{ count }}
</button>
```

Agar logic thoda complex ho, toh hum same scope mein functions declare karke un refs ko mutate kar sakte hain,  
aur un functions ko state ke saath methods ke roop mein expose kar sakte hain:

```js{7-10,15}
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // .value is needed in JavaScript
      count.value++
    }

    // don't forget to expose the function as well.
    return {
      count,
      increment
    }
  }
}
```

Exposed methods ko fir event handlers ke roop mein use kiya ja sakta hai:

```vue-html{1}
<button @click="increment">
  {{ count }}
</button>
```

Is example ko bina kisi build tools ke [Codepen](https://codepen.io/vuejs-examples/pen/WNYbaqo) par live dekh sakte ho.

### `<script setup>` \*\* {#script-setup}

Manually `setup()` ke through state aur methods ko expose karna kabhi-kabhi verbose lag sakta hai.  
Khushkismati se, agar aap [Single-File Components (SFCs)](/guide/scaling-up/sfc) use kar rahe ho,  
toh is process ko simplify kiya ja sakta hai `<script setup>` ke through:

```vue{1}
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNo9jUEKgzAQRa8yZKMiaNcllvYe2dgwQqiZhDhxE3L3jrW4/DPvv1/UK8Zhz6juSm82uciwIef4MOR8DImhQMIFKiwpeGgEbQwZsoE2BhsyMUwH0d66475ksuwCgSOb0CNx20ExBCc77POase8NVUN6PBdlSwKjj+vMKAlAvzOzWJ52dfYzGXXpjPoBAKX856uopDGeFfnq8XKp+gWq4FAi)

`<script setup>` ke andar declare kiye gaye top-level imports, variables aur functions automatically usi component ke template mein accessible hote hain. Aap template ko ek JavaScript function ki tarah samajh sakte ho jo same scope mein declared hota hai — isliye uske paas un saari cheezon ka access hota hai jo uske saath define ki gayi hain.

:::tip
Guide ke baaki parts mein Composition API ke code examples ke liye hum mainly SFC + `<script setup>` syntax ka use karenge,  
kyunki ye Vue developers ke beech sabse commonly used approach hai.

Agar aap SFC use nahi kar rahe ho, tab bhi aap Composition API ko [`setup()`](/api/composition-api-setup) option ke through use kar sakte ho.
:::

### Refs Kyun Use Karte Hain? \*\* {#why-refs}

Aap soch rahe honge ki hume plain variables ke bajaye `.value` ke saath refs ki zarurat kyun padti hai.  
Is baat ko samjhane ke liye hume Vue ke reactivity system ka thoda sa overview lena padega.

Jab aap kisi template mein ref ka use karte ho, aur baad mein us ref ka value change karte ho,  
toh Vue automatically us change ko detect karta hai aur DOM ko accordingly update karta hai.  
Yeh possible hota hai dependency-tracking based reactivity system ki wajah se.  
Jab koi component pehli baar render hota hai, Vue **track** karta hai har ref ko jo render ke dauraan use hua ho.  
Baad mein jab bhi wo ref mutate hota hai, toh yeh Vue ko **trigger** karta hai us component ko re-render karne ke liye jo us ref ko track kar raha tha.

Standard JavaScript mein, plain variables ke access ya mutation ko detect karne ka koi tarika nahi hota.  
Lekin kisi object ke properties ke get aur set operations ko hum intercept kar sakte hain using getter aur setter methods.

`.value` property Vue ko yeh mauka deti hai ki wo detect kar sake kab koi ref access ya mutate hua.  
Peeche ke level par, Vue getter mein tracking karta hai aur setter mein triggering.  
Conceptually, aap ref ko aise object ke roop mein soch sakte ho:

```js
// pseudo code, not actual implementation
const myRef = {
  _value: 0,
  get value() {
    track()
    return this._value
  },
  set value(newValue) {
    this._value = newValue
    trigger()
  }
}
```

Refs ka ek aur achha feature yeh hai ki plain variables ke comparison mein,  
aap refs ko functions mein pass kar sakte ho aur unka latest value access kar sakte ho —  
saath hi unka reactivity connection bhi maintain rehta hai.  
Yeh especially tab useful hota hai jab aap complex logic ko reusable code mein refactor kar rahe ho.

Reactivity system ko aur detail mein samjhaya gaya hai [Reactivity in Depth](/guide/extras/reactivity-in-depth) section mein.

</div>

<div class="options-api">

## Declaring Methods \* {#declaring-methods}

<VueSchoolLink href="https://vueschool.io/lessons/methods-in-vue-3" title="Free Vue.js Methods Lesson"/>

Component instance mein methods add karne ke liye hum `methods` option ka use karte hain.  
Yeh option ek aisa object hona chahiye jisme aap apne required methods define karte ho:

```js{7-11}
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    // methods can be called in lifecycle hooks, or other methods!
    this.increment()
  }
}
```

Vue automatically `methods` ke liye `this` ko bind karta hai taaki wo hamesha component instance ko refer kare.  
Isse yeh ensure hota hai ki method ka `this` value sahi rahe jab wo kisi event listener ya callback ke roop mein use kiya jaye.  
Aapko `methods` define karte waqt arrow functions ka use avoid karna chahiye,  
kyunki arrow functions `this` ko bind nahi karte, aur is wajah se Vue appropriate `this` assign nahi kar paata:

```js
export default {
  methods: {
    increment: () => {
      // BAD: yahaan `this` accessible nahi hoga!
    }
  }
}
```

Baaki component instance ki properties ki tarah, `methods` bhi component ke template mein accessible hote hain.  
Template ke andar yeh methods most commonly event listeners ke roop mein use kiye jaate hain:

```vue-html
<button @click="increment">{{ count }}</button>
```

[Try it in the Playground](https://play.vuejs.org/#eNplj9EKwyAMRX8l+LSx0e65uLL9hy+dZlTWqtg4BuK/z1baDgZicsPJgUR2d656B2QN45P02lErDH6c9QQKn10YCKIwAKqj7nAsPYBHCt6sCUDaYKiBS8lpLuk8/yNSb9XUrKg20uOIhnYXAPV6qhbF6fRvmOeodn6hfzwLKkx+vN5OyIFwdENHmBMAfwQia+AmBy1fV8E2gWBtjOUASInXBcxLvN4MLH0BCe1i4Q==)

Upar wale example mein, jab `<button>` par click hota hai tab `increment` method call hota hai.

</div>

### Deep Reactivity {#deep-reactivity}

<div class="options-api">

Vue mein state by default deeply reactive hoti hai. Iska matlab hai ki agar aap nested objects ya arrays mein bhi changes karte ho, toh wo bhi detect kiye jaate hain:

```js
export default {
  data() {
    return {
      obj: {
        nested: { count: 0 },
        arr: ['foo', 'bar']
      }
    }
  },
  methods: {
    mutateDeeply() {
      // these will work as expected.
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

Refs kisi bhi type ka value hold kar sakte hain, chahe wo deeply nested object ho, array ho, ya phir JavaScript ke built-in data structures jaise `Map`.

Ek ref apne value ko deeply reactive bana deta hai. Iska matlab hai ki agar aap nested objects ya arrays ko mutate karte ho,  
toh wo changes bhi detect kiye jaate hain:

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // these will work as expected.
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

Non-primitive values ko reactive proxies mein convert kiya jaata hai [`reactive()`](#reactive) ke through,  
jiske baare mein neeche discuss kiya gaya hai.

Aap deep reactivity se opt-out bhi kar sakte ho [shallow refs](/api/reactivity-advanced#shallowref) ka use karke.  
Shallow refs mein sirf `.value` access reactive hota hai.  
Yeh un cases mein useful hota hai jahan aapko large objects ke observation cost se bacha ke performance optimize karni ho,  
ya fir jab inner state kisi external library ke through manage ho rahi ho.

Aage padhne ke liye:

- [Large Immutable Structures ke liye Reactivity Overhead kam karna](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
- [External State Systems ke saath Integration](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

</div>

### DOM Update Timing {#dom-update-timing}

Jab aap reactive state ko mutate karte ho, toh DOM automatically update hota hai.  
Lekin dhyan rahe ki yeh updates synchronously apply nahi hote.  
Vue in updates ko buffer karta hai "next tick" tak —  
taaki har component sirf ek hi baar update ho, chahe kitne bhi state changes kiye gaye ho.

Agar aap kisi state change ke baad DOM update hone ka wait karna chahte ho,  
toh aap [nextTick()](/api/general#nexttick) global API ka use kar sakte ho:

<div class="composition-api">

```js
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // Now the DOM is updated
}
```

</div>
<div class="options-api">

```js
import { nextTick } from 'vue'

export default {
  methods: {
    async increment() {
      this.count++
      await nextTick()
      // Now the DOM is updated
    }
  }
}
```

</div>

<div class="composition-api">

## `reactive()` \*\* {#reactive}

Reactive state declare karne ka ek aur tareeka hai `reactive()` API ka use.  
Ref ke comparison mein jo inner value ko ek special object mein wrap karta hai,  
`reactive()` directly us object ko hi reactive bana deta hai:

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

> Dekhein: [Typing Reactive](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts" />

Template mein use:

```vue-html
<button @click="state.count++">
  {{ state.count }}
</button>
```

Reactive objects [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) hote hain  
aur normal objects ki tarah behave karte hain.  
Farq sirf itna hai ki Vue un reactive objects ke har property ke access aur mutation ko intercept kar sakta hai —  
jisse wo reactivity tracking aur triggering perform kar paata hai.

`reactive()` kisi object ko deeply convert karta hai:  
nested objects bhi jab access kiye jaate hain, toh unhe bhi `reactive()` ke through wrap kiya jaata hai.  
Jab ref ka value ek object hota hai, toh Vue internally `ref()` ke andar bhi `reactive()` call karta hai.  
Shallow refs ki tarah, aap deep reactivity se opt-out karne ke liye [`shallowReactive()`](/api/reactivity-advanced#shallowreactive) API ka use kar sakte ho.

### Reactive Proxy vs. Original \*\* {#reactive-proxy-vs-original-1}

Yeh samajhna zaroori hai ki `reactive()` se jo value return hoti hai,  
wo original object ka ek [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) hoti hai, aur wo original object ke barabar nahi hoti:

```js
const raw = {}
const proxy = reactive(raw)

// proxy is NOT equal to the original.
console.log(proxy === raw) // false
```

Sirf proxy hi reactive hoti hai — agar aap original object ko mutate karte ho, toh wo updates trigger nahi karega. Isliye Vue ke reactivity system ke saath kaam karte waqt best practice yeh hai ki aap **sirf apne state ke proxied versions ka hi use karein**.

Consistent proxy access ko ensure karne ke liye:

- Agar aap same object par baar-baar `reactive()` call karte ho, toh hamesha wahi same proxy return hoti hai.
- Agar aap already reactive proxy par `reactive()` dubara call karte ho, toh bhi wahi existing proxy return hoti hai:

```js
// calling reactive() on the same object returns the same proxy
console.log(reactive(raw) === proxy) // true

// calling reactive() on a proxy returns itself
console.log(reactive(proxy) === proxy) // true
```

Yeh rule nested objects par bhi apply hota hai. Deep reactivity ke kaaran, reactive object ke andar ke nested objects bhi proxy hote hain.

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### `reactive()` ki Limitations \*\* {#limitations-of-reactive}

`reactive()` API ke kuch limitations hote hain:

1. **Limited value types:**  
   Yeh sirf object types (jaise objects, arrays, aur [collection types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects#keyed_collections) jaise `Map` aur `Set`) ke liye kaam karta hai.  
   Yeh [primitive types](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) jaise `string`, `number` ya `boolean` ko hold nahi kar sakta.

2. **Pura object replace nahi kar sakte:**  
   Vue ka reactivity tracking property access ke upar depend karta hai.  
   Iska matlab hai ki hume reactive object ka reference same rakhna padta hai.  
   Agar aap reactive object ko "replace" karte ho, toh original reference se reactivity connection lost ho jaata hai:

   ```js
   let state = reactive({ count: 0 })

   // ab upar wala reference ({ count: 0 }) track nahi ho raha
   // (reactivity connection chala gaya!)
   state = reactive({ count: 1 })
   ```

3. **Destructure-friendly nahi hai:**  
   Jab hum reactive object ke primitive property ko destructure karke local variable mein le jaate hain,  
   ya kisi function ko pass karte hain, toh reactivity connection chala jaata hai:

   ```js
   const state = reactive({ count: 0 })

   // count ab state.count se disconnected hai
   let { count } = state
   count++ // original state affect nahi hota

   // function sirf plain number receive karega
   // aur state.count ke changes ko track nahi kar paayega
   callSomeFunction(state.count)
   ```

In limitations ki wajah se, hum yeh recommend karte hain ki reactive state declare karne ke liye `ref()` ko primary API ke roop mein use karo.

## Additional Ref Unwrapping Details \*\* {#additional-ref-unwrapping-details}

### Reactive Object Property ke roop mein \*\* {#ref-unwrapping-as-reactive-object-property}

Jab koi ref reactive object ke property ke roop mein use hota hai,  
toh usse access ya mutate karte waqt automatic unwrapping ho jaata hai.  
Yeh ek normal property ki tarah behave karta hai:

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

Agar aap kisi property par ek naya ref assign karte ho jo pehle se ek ref se linked thi,  
toh wo purane ref ko replace kar deta hai:

```js
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// original ref ab state.count se disconnected ho gaya
console.log(count.value) // 1
```

Ref unwrapping sirf tab hota hai jab ref ek deep reactive object ke andar ho.  
Yeh unwrapping [shallow reactive object](/api/reactivity-advanced#shallowreactive) ke andar apply nahi hoti.

### Arrays aur Collections mein Caveat \*\* {#caveat-in-arrays-and-collections}

Reactive objects ke unlike,  
jab ref kisi reactive array ya native collection jaise `Map` ke element ke roop mein access kiya jaata hai,  
toh **koi unwrapping nahi hoti**:

```js
const books = reactive([ref('Vue 3 Guide')])
// need .value here
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// need .value here
console.log(map.get('count').value)
```

### Template mein Unwrapping ka Caveat \*\* {#caveat-when-unwrapping-in-templates}

Template mein ref unwrapping sirf tab apply hoti hai jab ref template render context mein top-level property ho.

Neeche wale example mein, `count` aur `object` top-level properties hain,  
lekin `object.id` top-level nahi hai:

```js
const count = ref(0)
const object = { id: ref(1) }
```

Isliye, yeh expression expected tarike se kaam karta hai:

```vue-html
{{ count + 1 }}
```

...lekin yeh wala **kaam nahi karega**:

```vue-html
{{ object.id + 1 }}
```

Iska rendered result hoga `[object Object]1`,  
kyunki `object.id` ko evaluate karte waqt unwrap nahi kiya jaata,  
aur wo ref object hi bana rehta hai.  
Isse fix karne ke liye hum `id` ko destructure karke ek top-level property bana sakte hain:

```js
const { id } = object
```

```vue-html
{{ id + 1 }}
```

Ab render result hoga `2`.

Ek aur important baat ye hai ki agar koi ref expression ka final evaluated value hota hai  
text interpolation (yaani <code v-pre>{{ }}</code>) ke andar,  
toh wo unwrap ho jaata hai — isliye ye render karega `1`:

```vue-html
{{ object.id }}
```

Ye sirf text interpolation ka ek convenience feature hai  
aur iske barabar hai: <code v-pre>{{ object.id.value }}</code>

</div>

<div class="options-api">

### Stateful Methods \* {#stateful-methods}

Kuch cases mein hume dynamically ek method function create karna padta hai,  
jaise debounced event handler banate waqt:

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // Lodash se debouncing
    click: debounce(function () {
      // ... click ka response ...
    }, 500)
  }
}
```

Lekin yeh approach problematic hoti hai jab component multiple baar reuse ho raha ho,  
kyunki debounced function **stateful** hota hai:  
uske andar kuch internal timing state maintained hoti hai.  
Agar multiple component instances same debounced function share karte hain,  
toh wo ek dusre ko interfere karenge.

Is problem ko avoid karne ke liye,  
har component instance ke liye debounced function `created` lifecycle hook ke andar create karo:

```js
export default {
  created() {
    // ab har instance ke paas apna independent debounced handler hoga
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // component remove hone par timer cancel karna bhi best practice hai
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... click ka response ...
    }
  }
}
```

</div>
