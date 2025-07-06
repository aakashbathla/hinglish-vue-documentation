# Computed Properties {#computed-properties}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/computed-properties-in-vue-3" title="Free Vue.js Computed Properties Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-computed-properties-in-vue-with-the-composition-api" title="Free Vue.js Computed Properties Lesson"/>
</div>

## Basic Example {#basic-example}

Template ke andar expressions use karna kaafi convenient hota hai, lekin yeh sirf simple operations ke liye hi theek hai. Agar aap apna logic template mein zyada likhne lag jao to wo messy aur difficult to maintain ban jaata hai. Jaise agar humare paas ek object hai jisme ek nested array hai:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  }
}
```

</div>
<div class="composition-api">

```js
const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})
```

</div>

Aur hume yeh dikhana hai ki author ne koi book publish ki hai ya nahi:

```vue-html
<p>Has published books:</p>
<span>{{ author.books.length > 0 ? 'Yes' : 'No' }}</span>
```

Is point par, template thoda cluttered lagne lagta hai. Ek second lagta hai samajhne mein ki yeh ek condition check kar raha hai `author.books` par based. Aur agar hume yehi calculation baar-baar karna pade to repeating logic ho jaayega.

Isi liye, jab logic thoda complex ho aur reactive data ke saath ho, tab use karna chahiye **computed property**. Yeh wahi example hai, thoda better structure ke saath:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
  computed: {
    // ek computed getter
    publishedBooksMessage() {
      // `this` component instance ko point karta hai
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}
```

```vue-html
<p>Has published books:</p>
<span>{{ publishedBooksMessage }}</span>
```

[Playground mein try karo](https://play.vuejs.org/#eNqFkN1KxDAQhV/l0JsqaFfUq1IquwiKsF6JINaLbDNui20S8rO4lL676c82eCFCIDOZMzkzXxetlUoOjqI0ykypa2XzQtC3ktqC0ydzjUVXCIAzy87OpxjQZJ0WpwxgzlZSp+EBEKylFPGTrATuJcUXobST8sukeA8vQPzqCNe4xJofmCiJ48HV/FfbLLrxog0zdfmn4tYrXirC9mgs6WMcBB+nsJ+C8erHH0rZKmeJL0sot2tqUxHfDONuyRi2p4BggWCr2iQTgGTcLGlI7G2FHFe4Q/xGJoYn8SznQSbTQviTrRboPrHUqoZZ8hmQqfyRmTDFTC1bqalsFBN5183o/3NG33uvoWUwXYyi/gdTEpwK)

Yahan humne ek computed property banayi hai `publishedBooksMessage`.

Agar aap `books` array ka value change karoge to `publishedBooksMessage` ka output bhi change ho jaayega.

Templates mein computed properties ko bilkul normal properties ki tarah bind kiya ja sakta hai. Vue ko pata hota hai ki `this.publishedBooksMessage` depend karta hai `this.author.books` par, isliye agar `this.author.books` change hota hai to `this.publishedBooksMessage` bhi update ho jaayega.

Aur dekho: [Typing Computed Properties](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// ek computed ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

[Playground mein try karo](https://play.vuejs.org/#eNp1kE9Lw0AQxb/KI5dtoTainkoaaREUoZ5EEONhm0ybYLO77J9CCfnuzta0vdjbzr6Zeb95XbIwZroPlMySzJW2MR6OfDB5oZrWaOvRwZIsfbOnCUrdmuCpQo+N1S0ET4pCFarUynnI4GttMT9PjLpCAUq2NIN41bXCkyYxiZ9rrX/cDF/xDYiPQLjDDRbVXqqSHZ5DUw2tg3zP8lK6pvxHe2DtvSasDs6TPTAT8F2ofhzh0hTygm5pc+I1Yb1rXE3VMsKsyDm5JcY/9Y5GY8xzHI+wnIpVw4nTI/10R2rra+S4xSPEJzkBvvNNs310ztK/RDlLLjy1Zic9cQVkJn+R7gIwxJGlMXiWnZEq77orhH3Pq2NH9DjvTfpfSBSbmA==)

Yahan humne `publishedBooksMessage` naam ka ek computed property banaya hai. `computed()` function ek [getter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description) leta hai aur return karta hai ek **computed ref**. Normal refs ki tarah hi aap iska value `publishedBooksMessage.value` se access kar sakte ho. Lekin templates mein `.value` lagane ki zarurat nahi hoti — Vue automatically unwrap kar deta hai.

Computed property apne reactive dependencies ko automatically track karta hai. Vue ko pata hai ki `publishedBooksMessage` ka calculation `author.books` par depend karta hai, isliye jab bhi `author.books` change hoga, related bindings automatically update ho jaayenge.

Aur dekho: [Typing Computed](/guide/typescript/composition-api#typing-computed) <sup class="vt-badge ts" />

</div>

## Computed Caching vs. Methods {#computed-caching-vs-methods}

Aapne notice kiya hoga ki hum wahi result ek method ke through bhi le sakte hain:

```vue-html
<p>{{ calculateBooksMessage() }}</p>
```

<div class="options-api">

```js
// component ke andar
methods: {
  calculateBooksMessage() {
    return this.author.books.length > 0 ? 'Yes' : 'No'
  }
}
```

</div>

<div class="composition-api">

```js
// component ke andar
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Yes' : 'No'
}
```

</div>

Computed property ki jagah, hum wahi function ek method ke roop mein define kar sakte hain. Dono approaches ka output toh same hota hai, lekin main difference yeh hai ki **computed properties apni reactive dependencies ke base par cache hoti hain.** Ek computed property tabhi re-calculate hoti hai jab uski reactive dependency change hoti hai. Iska matlab yeh hua ki jab tak `author.books` change nahi hota, `publishedBooksMessage` ko jitni baar bhi access karo, wo pehle se computed value hi return karega — bina getter ko dubara chalaye.

Iska ek example yeh hai — neeche wala computed property kabhi update nahi hoga, kyunki `Date.now()` ek reactive dependency nahi hai:

<div class="options-api">

```js
computed: {
  now() {
    return Date.now()
  }
}
```

</div>

<div class="composition-api">

```js
const now = computed(() => Date.now())
```

</div>

Wahin agar aap isko method ke through call karte ho, toh wo **har render pe** dubara execute hoga.

Ab caching zaroori kyu hai? Sochiye aapke paas ek heavy computed property hai `list`, jo ek bade array ko loop karke kuch heavy calculation karta hai. Ab agar dusri computed properties bhi `list` pe dependent hain, toh bina caching ke, har bar wo costly `list` computation bar-bar chalega. Isliye, jahan caching nahi chahiye, wahan method use karna better hai.

## Writable Computed {#writable-computed}

By default, computed properties sirf read-only hoti hain. Agar aap kisi computed property ko assign karne ki koshish karte ho, toh runtime warning milti hai. Lekin kuch rare cases mein agar aapko ek "writable" computed property chahiye, toh aap uske liye ek getter aur setter dono define kar sakte ho:

<div class="options-api">

```js
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: {
      // getter
      get() {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set(newValue) {
        // Yahan hum destructuring assignment ka use kar rahe hain
        ;[this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

Ab jab aap `this.fullName = 'John Doe'` likhoge, toh setter call hoga aur `firstName` aur `lastName` update ho jayenge.

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // Yahan hum destructuring assignment ka use kar rahe hain
    ;[firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

Ab jab aap `fullName.value = 'John Doe'` likhoge, toh setter call hoga aur `firstName` aur `lastName` update ho jayenge.

</div>

## Getting the Previous Value {#previous}

- Sirf Vue 3.4+ mein supported hai

<p class="options-api">
Agar aapko zarurat pade, toh computed property ke getter ke dusre argument se previous value le sakte ho:
</p>

<p class="composition-api">
Agar aapko zarurat pade, toh computed property ke getter ke pehle argument se previous value le sakte ho:
</p>

<div class="options-api">

```js
export default {
  data() {
    return {
      count: 2
    }
  },
  computed: {
    // Ye computed tab tak count ki value return karega jab tak wo 3 ya usse kam hai.
    // Jab count 4 ya usse zyada ho jata hai, toh pehle wali value return hoti hai
    // jab tak count wapas 3 ya usse kam na ho jaye
    alwaysSmall(_, previous) {
      if (this.count <= 3) {
        return this.count
      }

      return previous
    }
  }
}
```

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(2)

// Ye computed tab tak count ki value return karega jab tak wo 3 ya usse kam hai.
// Jab count 4 ya usse zyada ho jata hai, toh pehle wali value return hoti hai
// jab tak count wapas 3 ya usse kam na ho jaye
const alwaysSmall = computed((previous) => {
  if (count.value <= 3) {
    return count.value
  }

  return previous
})
</script>
```

</div>

Agar aap writable computed ka use kar rahe ho:

<div class="options-api">

```js
export default {
  data() {
    return {
      count: 2
    }
  },
  computed: {
    alwaysSmall: {
      get(_, previous) {
        if (this.count <= 3) {
          return this.count
        }

        return previous
      },
      set(newValue) {
        this.count = newValue * 2
      }
    }
  }
}
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(2)

const alwaysSmall = computed({
  get(previous) {
    if (count.value <= 3) {
      return count.value
    }

    return previous
  },
  set(newValue) {
    count.value = newValue * 2
  }
})
</script>
```

</div>

## Best Practices {#best-practices}

### Getters side-effect se free hone chahiye {#getters-should-be-side-effect-free}

Ye yaad rakhna zaroori hai ki computed getter functions sirf pure calculation karein aur side effects se free ho. Jaise ki **kisi aur state ko mutate mat karo, async requests mat bhejo, ya computed getter ke andar DOM mutate mat karo!** Computed property ka kaam ye batana hai ki kisi value ko dusri values ke base pe kaise derive karein — iska sirf yehi kaam hona chahiye. Aage guide mein hum dekhenge ki state change hone par side effects kaise handle karein using [watchers](./watchers).

### Computed value ko mutate mat karo {#avoid-mutating-computed-value}

Computed property ka return value ek derived state hota hai. Use ek temporary snapshot ki tarah samjho — har baar jab source state change hoti hai, ek naya snapshot ban jaata hai. Is snapshot ko mutate karne ka koi matlab nahi banta, isliye computed value ko read-only treat karo. Agar kuchh change karna ho, toh us source state ko update karo jisse computed value depend karti hai, taki wo automatic re-calculate ho jaaye.
