# Watchers {#watchers}

## Basic Example {#basic-example}

Computed properties humein declaratively derived values compute karne ki suvidha deti hain. Lekin kuch cases me humein kisi state change ke response me "side effects" perform karne ki zarurat padti hai — jaise ki DOM ko mutate karna, ya kisi async operation ke result ke base par kisi doosri state ko change karna.

<div class="options-api">

Options API ke saath, hum [`watch` option](/api/options-state#watch) ka use karke koi function trigger kar sakte hain jab bhi koi reactive property change hoti hai:

```js
export default {
  data() {
    return {
      question: '',
      answer: 'Questions usually contain a question mark. ;-)',
      loading: false
    }
  },
  watch: {
    // whenever question changes, this function will run
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      this.loading = true
      this.answer = 'Thinking...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer
      } catch (error) {
        this.answer = 'Error! Could not reach the API. ' + error
      } finally {
        this.loading = false
      }
    }
  }
}
```

```vue-html
<p>
  Ask a yes/no question:
  <input v-model="question" :disabled="loading" />
</p>
<p>{{ answer }}</p>
```

[Try it in the Playground](https://play.vuejs.org/#eNp9VE1v2zAM/SucLnaw1D70lqUbsiKH7rB1W4++aDYdq5ElTx9xgiD/fbT8lXZFAQO2+Mgn8pH0mW2aJjl4ZCu2trkRjfucKTw22jgosOReOjhnCqDgjseL/hvAoPNGjSeAvx6tE1qtIIqWo5Er26Ih088BteCt51KeINfKcaGAT5FQc7NP4NPNYiaQmhdC7VZQcmlxMF+61yUcWu7yajVmkabQVqjwgGZmzSuudmiX4CphofQqD+ZWSAnGqz5y9I4VtmOuS9CyGA9T3QCihGu3RKhc+gJtHH2JFld+EG5Mdug2QYZ4MSKhgBd11OgqXdipEm5PKoer0Jk2kA66wB044/EF1GtOSPRUCbUnryRJosnFnK4zpC5YR7205M9bLhyUSIrGUeVcY1dpekKrdNK6MuWNiKYKXt8V98FElDxbknGxGLCpZMi7VkGMxmjzv0pz1tvO4QPcay8LULoj5RToKoTN40MCEXyEQDJTl0KFmXpNOqsUxudN+TNFzzqdJp8ODutGcod0Alg34QWwsXsaVtIjVXqe9h5bC9V4B4ebWhco7zI24hmDVSEs/yOxIPOQEFnTnjzt2emS83nYFrhcevM6nRJhS+Ys9aoUu6Av7WqoNWO5rhsh0fxownplbBqhjJEmuv0WbN2UDNtDMRXm+zfsz/bY2TL2SH1Ec8CMTZjjhqaxh7e/v+ORvieQqvaSvN8Bf6HV0veSdG5fvSoo7Su/kO1D3f13SKInuz06VHYsahzzfl0yRj+s+3dKn9O9TW7HPrPLP624lFU=)

`watch` option dot-delimited path ko bhi key ke roop me support karta hai:

```js
export default {
  watch: {
    // Note: only simple paths. Expressions are not supported.
    'some.nested.key'(newValue) {
      // ...
    }
  }
}
```

</div>

<div class="composition-api">

Composition API ke saath, hum [`watch` function](/api/reactivity-core#watch) ka use karke ek callback trigger kar sakte hain jab bhi koi reactive state change hoti hai:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Questions usually contain a question mark. ;-)')
const loading = ref(false)

// watch works directly on a ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.includes('?')) {
    loading.value = true
    answer.value = 'Thinking...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    } finally {
      loading.value = false
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" :disabled="loading" />
  </p>
  <p>{{ answer }}</p>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNp9U8Fy0zAQ/ZVFF9tDah96C2mZ0umhHKBAj7oIe52oUSQjyXEyGf87KytyoDC9JPa+p+e3b1cndtd15b5HtmQrV1vZeXDo++6Wa7nrjPVwAovtAgbh6w2M0Fqzg4xOZFxzXRvtPPzq0XlpNNwEbp5lRUKEdgPaVP925jnoXS+UOgKxvJAaxEVjJ+y2hA9XxUVFGdFIvT7LtEI5JIzrqjrbGozdOmikxdqTKqmIQOV6gvOkvQDhjrqGXOOQvCzAqCa9FHBzCyeuAWT7F6uUulZ9gy7PPmZFETmQjJV7oXoke972GJHY+Axkzxupt4FalhRcYHh7TDIQcqA+LTriikFIDy0G59nG+84tq+qITpty8G0lOhmSiedefSaPZ0mnfHFG50VRRkbkj1BPceVorbFzF/+6fQj4O7g3vWpAm6Ao6JzfINw9PZaQwXuYNJJuK/U0z1nxdTLT0M7s8Ec/I3WxquLS0brRi8ddp4RHegNYhR0M/Du3pXFSAJU285osI7aSuus97K92pkF1w1nCOYNlI534qbCh8tkOVasoXkV1+sjplLZ0HGN5Vc1G2IJ5R8Np5XpKlK7J1CJntdl1UqH92k0bzdkyNc8ZRWGGz1MtbMQi1esN1tv/1F/cIdQ4e6LJod0jZzPmhV2jj/DDjy94oOcZpK57Rew3wO/ojOpjJIH2qdcN2f6DN7l9nC47RfTsHg4etUtNpZUeJz5ndPPv32j9Yve6vE6DZuNvu1R2Tg==)

### Watch Source Types {#watch-source-types}

`watch` ka pehla argument alag-alag types ke reactive "sources" ho sakte hain: ye ek ref (including computed refs), ek reactive object, ek [getter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description), ya multiple sources ka array ho sakta hai:

```js
const x = ref(0)
const y = ref(0)

// single ref
watch(x, (newX) => {
  console.log(`x is ${newX}`)
})

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`)
  }
)

// array of multiple sources
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`)
})
```

Dhyan rahe ki aap ek reactive object ki property ko seedhe is tarah se watch nahi kar sakte:

```js
const obj = reactive({ count: 0 })

// this won't work because we are passing a number to watch()
watch(obj.count, (count) => {
  console.log(`Count is: ${count}`)
})
```

Iski jagah, ek getter function ka use karein:

```js
// instead, use a getter:
watch(
  () => obj.count,
  (count) => {
    console.log(`Count is: ${count}`)
  }
)
```

</div>

## Deep Watchers {#deep-watchers}

<div class="options-api">

By default, `watch` shallow hota hai: callback sirf tab trigger hota hai jab watched property ko ek nayi value assign ki jaati hai — ye nested property changes pe trigger nahi hota. Agar aap chahte ho ki callback har nested mutation pe bhi fire ho, toh aapko ek deep watcher use karna hoga:

```js
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // Note: `newValue` will be equal to `oldValue` here
        // on nested mutations as long as the object itself
        // hasn't been replaced.
      },
      deep: true
    }
  }
}
```

</div>

<div class="composition-api">

Jab aap `watch()` ko kisi reactive object par directly call karte ho, toh ye implicitly ek deep watcher create karta hai — callback har nested mutation par trigger hoga:

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // fires on nested property mutations
  // Note: `newValue` will be equal to `oldValue` here
  // because they both point to the same object!
})

obj.count++
```

Isse differentiate karna zaroori hai ek aise getter se jo ek reactive object return karta hai — uss case me callback sirf tab fire hoga jab getter ek alag object return karega:

```js
watch(
  () => state.someObject,
  () => {
    // fires only when state.someObject is replaced
  }
)
```

Lekin aap second case ko forcefully deep watcher bana sakte hain by explicitly `deep` option ka use karke:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // Note: `newValue` will be equal to `oldValue` here
    // *unless* state.someObject has been replaced
  },
  { deep: true }
)
```

</div>

Vue 3.5+ mein, `deep` option ek number bhi ho sakta hai jo max traversal depth ko indicate karta hai — matlab Vue ek object ke nested properties ko kitne levels tak traverse kare.

:::warning Use with Caution
Deep watch ka matlab hota hai watched object ke saare nested properties ko traverse karna, jo bade data structures ke saath expensive ho sakta hai. Iska use sirf tab karein jab zaroori ho, aur performance implications ka dhyan rakhein.
:::

## Eager Watchers {#eager-watchers}

By default, `watch` lazy hota hai: callback tab tak trigger nahi hota jab tak watched source me change na ho. Lekin kuch cases me humein wahi callback logic eagerly run karwana hota hai — jaise agar humein kuch initial data fetch karna ho, aur baad me relevant state change hone par dobara fetch karna ho.

<div class="options-api">

Hum ek watcher ka callback turant execute karwa sakte hain by object syntax ka use karke jisme `handler` function aur `immediate: true` option ho:

```js
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // this will be run immediately on component creation.
      },
      // force eager callback execution
      immediate: true
    }
  }
  // ...
}
```

Handler function ka initial execution `created` hook se thoda pehle hota hai. Us waqt tak Vue `data`, `computed`, aur `methods` options ko process kar chuka hota hai, isliye ye properties first invocation me available hoti hain.

</div>

<div class="composition-api">

Hum watcher ka callback turant execute karwa sakte hain by `immediate: true` option pass karke:

```js
watch(
  source,
  (newValue, oldValue) => {
    // executed immediately, then again when `source` changes
  },
  { immediate: true }
)
```

</div>

## Once Watchers {#once-watchers}

- Sirf Vue 3.4+ mein supported hai

Watcher ka callback har baar watched source change hone par execute hota hai. Agar aap chahte ho ki callback sirf ek hi baar trigger ho jab source change ho, toh `once: true` option ka use karein.

<div class="options-api">

```js
export default {
  watch: {
    source: {
      handler(newValue, oldValue) {
        // when `source` changes, triggers only once
      },
      once: true
    }
  }
}
```

</div>

<div class="composition-api">

```js
watch(
  source,
  (newValue, oldValue) => {
    // when `source` changes, triggers only once
  },
  { once: true }
)
```

</div>

<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

Yeh kaafi common hai ki watcher ka callback bilkul wahi reactive state use kare jo source hota hai. Jaise ki neeche diye gaye code me dekhein, jahan ek watcher ka use kiya gaya hai ek remote resource ko load karne ke liye jab bhi `todoId` ref change hota hai:

```js
const todoId = ref(1)
const data = ref(null)

watch(
  todoId,
  async () => {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    )
    data.value = await response.json()
  },
  { immediate: true }
)
```

Khaaskar dhyan dein ki watcher me `todoId` do jagah use ho raha hai — ek baar source ke roop me aur doosri baar callback ke andar.

Isko hum [`watchEffect()`](/api/reactivity-core#watcheffect) ke saath simplify kar sakte hain. `watchEffect()` humein callback ke reactive dependencies ko automatically track karne ki suvidha deta hai. Upar wale watcher ko hum is tarah se likh sakte hain:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

Yahaan, callback turant run karega — `immediate: true` specify karne ki koi zarurat nahi hai. Iske execution ke dauraan, `todoId.value` ko automatically ek dependency ke roop me track kiya jaayega (computed properties jaise). Jab bhi `todoId.value` change hoga, callback dobara run hoga. `watchEffect()` ke saath, humein `todoId` ko explicitly source value ke roop me pass karne ki zarurat nahi padti.

Aap [ye example](/examples/#fetching-data) dekh sakte hain jisme `watchEffect()` aur reactive data-fetching ka actual use dikhaya gaya hai.

Aise examples jisme sirf ek hi dependency ho, wahan `watchEffect()` ka benefit chhota hota hai. Lekin jab multiple dependencies hoti hain, `watchEffect()` use karne se humein manually dependencies list maintain karne ki zarurat nahi padti. Saath hi, agar aapko kisi nested data structure ke multiple properties ko watch karna ho, toh `watchEffect()` deep watcher ke comparison me zyada efficient ho sakta hai, kyunki ye sirf wahi properties track karta hai jo callback ke andar actually use ho rahi hoti hain — sabko recursively track nahi karta.

:::tip
`watchEffect` sirf apne **synchronous** execution ke dauraan dependencies ko track karta hai. Agar aap ise async callback ke saath use kar rahe hain, toh sirf `await` se pehle access ki gayi properties hi track hongi.
:::

### `watch` vs. `watchEffect` {#watch-vs-watcheffect}

`watch` aur `watchEffect` dono humein reactively side effects perform karne ki suvidha dete hain. In dono ke beech main difference unka dependency tracking ka tareeka hai:

- `watch` sirf explicitly diye gaye source ko track karta hai. Ye callback ke andar access ki gayi properties ko track nahi karta. Aur ye callback tabhi trigger hota hai jab source actually change hota hai. `watch` dependency tracking ko side effect se alag rakhta hai, jo humein precise control deta hai ki callback kab fire hona chahiye.

- `watchEffect`, doosri taraf, dependency tracking aur side effect dono ko ek hi phase me combine karta hai. Ye apne synchronous execution ke dauraan access ki gayi har reactive property ko automatically track karta hai. Ye zyada convenient hota hai aur code concise banata hai, lekin reactive dependencies thodi less explicit ho jaati hain.

</div>

## Side Effect Cleanup {#side-effect-cleanup}

Kabhi-kabhi hum watcher ke andar side effects perform karte hain, jaise ki asynchronous requests:

<div class="composition-api">

```js
watch(id, (newId) => {
  fetch(`/api/${newId}`).then(() => {
    // callback logic
  })
})
```

</div>
<div class="options-api">

```js
export default {
  watch: {
    id(newId) {
      fetch(`/api/${newId}`).then(() => {
        // callback logic
      })
    }
  }
}
```

</div>

Lekin agar `id` usse pehle hi change ho jaaye jab tak request complete hoti hai, toh kya hoga? Jab pehli request complete hoti hai, tab bhi wo callback ko ek aise ID ke saath fire karegi jo ab purani (stale) ho chuki hai. Ideally, hum chahenge ki jab `id` naya ho jaaye, toh purani request ko cancel kar diya jaaye.

Iske liye hum [`onWatcherCleanup()`](/api/reactivity-core#onwatchercleanup) <sup class="vt-badge" data-text="3.5+" /> API ka use kar sakte hain, jisse ek cleanup function register kiya ja sakta hai. Ye function tab call hota hai jab watcher invalidate hone wala ho ya dobara run hone ja raha ho:

<div class="composition-api">

```js {10-13}
import { watch, onWatcherCleanup } from 'vue'

watch(id, (newId) => {
  const controller = new AbortController()

  fetch(`/api/${newId}`, { signal: controller.signal }).then(() => {
    // callback logic
  })

  onWatcherCleanup(() => {
    // abort stale request
    controller.abort()
  })
})
```

</div>
<div class="options-api">

```js {12-15}
import { onWatcherCleanup } from 'vue'

export default {
  watch: {
    id(newId) {
      const controller = new AbortController()

      fetch(`/api/${newId}`, { signal: controller.signal }).then(() => {
        // callback logic
      })

      onWatcherCleanup(() => {
        // abort stale request
        controller.abort()
      })
    }
  }
}
```

</div>

Dhyan dein ki `onWatcherCleanup` sirf Vue 3.5+ mein supported hai aur isse `watchEffect` ke effect function ya `watch` ke callback function ke synchronous execution ke dauraan hi call kiya ja sakta hai — aap ise async function ke andar kisi `await` ke baad call nahi kar sakte.

Ek alternative ke roop me, ek `onCleanup` function bhi watcher callbacks ko 3rd argument ke roop me diya jaata hai<span class="composition-api">, aur `watchEffect` ke effect function me ye pehla argument hota hai</span>:

<div class="composition-api">

```js
watch(id, (newId, oldId, onCleanup) => {
  // ...
  onCleanup(() => {
    // cleanup logic
  })
})

watchEffect((onCleanup) => {
  // ...
  onCleanup(() => {
    // cleanup logic
  })
})
```

</div>
<div class="options-api">

```js
export default {
  watch: {
    id(newId, oldId, onCleanup) {
      // ...
      onCleanup(() => {
        // cleanup logic
      })
    }
  }
}
```

</div>

Ye approach Vue 3.5 se pehle ke versions me kaam karti hai. Saath hi, function argument ke through diya gaya `onCleanup` watcher instance ke saath bound hota hai, isliye ye `onWatcherCleanup` ki tarah strictly synchronous constraint ka paalan nahi karta.

## Callback Flush Timing {#callback-flush-timing}

Jab aap reactive state ko mutate karte ho, toh ye Vue component updates aur aapke dwara banaye gaye watcher callbacks dono ko trigger kar sakta hai.

Component updates ki tarah, user-created watcher callbacks bhi batch kiye jaate hain taaki duplicate invocations avoid ho sakein. For example, agar aap ek watched array me synchronously 1000 items push karte hain, toh hum nahi chahenge ki watcher 1000 baar trigger ho.

By default, watcher ka callback **parent component updates ke baad** (agar koi hai) aur **owner component ke DOM update hone se pehle** call hota hai. Iska matlab hai ki agar aap watcher callback ke andar owner component ke DOM ko access karne ki koshish karte ho, toh wo DOM abhi pre-update state me hoga.

### Post Watchers {#post-watchers}

Agar aap chahte hain ki watcher callback ke andar owner component ka DOM tab access ho jab Vue usse update kar chuka ho, toh aapko `flush: 'post'` option specify karna hoga:

<div class="options-api">

```js{6}
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'post'
    }
  }
}
```

</div>

<div class="composition-api">

```js{2,6}
watch(source, callback, {
  flush: 'post'
})

watchEffect(callback, {
  flush: 'post'
})
```

Post-flush `watchEffect()` ke liye ek convenient alias bhi available hai: `watchPostEffect()`:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* executed after Vue updates */
})
```

</div>

### Sync Watchers {#sync-watchers}

Aap ek aisa watcher bhi create kar sakte hain jo synchronously fire ho — kisi bhi Vue-managed update se pehle:

<div class="options-api">

```js{6}
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'sync'
    }
  }
}
```

</div>

<div class="composition-api">

```js{2,6}
watch(source, callback, {
  flush: 'sync'
})

watchEffect(callback, {
  flush: 'sync'
})
```

Synchronous `watchEffect()` ke liye bhi ek convenient alias available hai: `watchSyncEffect()`:

```js
import { watchSyncEffect } from 'vue'

watchSyncEffect(() => {
  /* executed synchronously upon reactive data change */
})
```

</div>

:::warning Use with Caution
Synchronous watchers batching ka use nahi karte aur har baar jab koi reactive mutation detect hota hai, turant trigger ho jaate hain. Aap inhe simple boolean values ke liye use kar sakte ho, lekin un data sources pe use karne se bachein jo synchronously baar-baar mutate ho sakte hain — jaise arrays.
:::

<div class="options-api">

## `this.$watch()` \* {#this-watch}

Aap imperatively bhi watchers create kar sakte hain [`$watch()` instance method](/api/component-instance#watch) ka use karke:

```js
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

Ye tab useful hota hai jab aapko conditionally watcher setup karna ho, ya sirf user interaction ke response me kisi cheez ko watch karna ho. Is method se aap watcher ko manually jaldi stop bhi kar sakte ho.

</div>

## Stopping a Watcher {#stopping-a-watcher}

<div class="options-api">

`watch` option ya `$watch()` instance method se declare kiye gaye watchers automatically stop ho jaate hain jab owner component unmount hota hai, isliye zyadatar cases me aapko manually watcher ko stop karne ki chinta nahi karni padti.

Lekin agar kisi rare case me aapko watcher ko component ke unmount hone se pehle hi stop karna ho, toh `$watch()` API ek function return karti hai jiska use karke aap watcher ko stop kar sakte ho:

```js
const unwatch = this.$watch('foo', callback)

// ...when the watcher is no longer needed:
unwatch()
```

</div>

<div class="composition-api">

`setup()` ya `<script setup>` ke andar synchronously declare kiye gaye watchers owner component instance ke saath bind hote hain, aur jab owner component unmount hota hai, tab ye automatically stop ho jaate hain. Isliye zyadatar cases me aapko manually watcher ko stop karne ki zarurat nahi hoti.

Yahaan key point ye hai ki watcher **synchronously** create hona chahiye: agar aap watcher ko kisi async callback ke andar create karte ho, toh wo owner component ke saath bind nahi hota, aur usse manually stop karna padta hai taaki memory leak na ho. Yahaan ek example diya gaya hai:

```vue
<script setup>
import { watchEffect } from 'vue'

// this one will be automatically stopped
watchEffect(() => {})

// ...this one will not!
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

Watcher ko manually stop karne ke liye, uske saath return hone wale handle function ka use karein. Ye `watch` aur `watchEffect` dono ke liye kaam karta hai:

```js
const unwatch = watchEffect(() => {})

// ...later, when no longer needed
unwatch()
```

Dhyan rahe ki aise cases bahut kam hote hain jahan aapko watchers asynchronously create karne ki zarurat padti hai. Jahan tak ho sake, synchronous creation ko hi prefer karna chahiye. Agar aapko kisi async data ka wait karna ho, toh aap apne watch logic ko conditionally likh sakte hain:

```js
// data to be loaded asynchronously
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // do something when data is loaded
  }
})
```

</div>
