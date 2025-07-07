# Provide / Inject {#provide-inject}

> Yeh page assume karta hai ki aap pehle hi [Components Basics](/guide/essentials/component-basics) padh chuke ho. Agar aap components ke liye naye ho to pehle woh padho.

## Prop Drilling {#prop-drilling}

Aksar jab humein parent se child component tak data bhejna hota hai, to hum [props](/guide/components/props) ka use karte hain. Lekin socho agar ek bade component tree mein koi deeply nested component ko ek dur ke ancestor component se kuch data chahiye ho. Sirf props ke zariye, humein wohi prop poore parent chain mein pass karna padega:

![prop drilling diagram](./images/prop-drilling.png)

<!-- https://www.figma.com/file/yNDTtReM2xVgjcGVRzChss/prop-drilling -->

Dhyan do ki `<Footer>` component ko shayad un props se koi matlab na ho, phir bhi use declare karke aage pass karna padega sirf isliye taaki `<DeepChild>` unko access kar sake. Agar parent chain aur lambi ho to aur bhi components is process mein affect honge. Isko hi "props drilling" kehte hain aur yeh kaafi annoying ho sakta hai.

Hum is props drilling ko `provide` aur `inject` ke zariye solve kar sakte hain. Ek parent component apne sabhi descendants ke liye ek **dependency provider** ki tarah kaam kar sakta hai. Koi bhi component descendant tree mein, chahe jitna bhi deep ho, **inject** kar sakta hai woh dependency jo parent chain mein kisi component ne provide ki ho.

![Provide/inject scheme](./images/provide-inject.png)

<!-- https://www.figma.com/file/PbTJ9oXis5KUawEOWdy2cE/provide-inject -->

## Provide {#provide}

<div class="composition-api">

Agar aapko apne component ke descendants ko data provide karna hai, to [`provide()`](/api/composition-api-dependency-injection#provide) function ka use karo:

```vue
<script setup>
import { provide } from 'vue'

provide(/* key */ 'message', /* value */ 'hello!')
</script>
```

Agar aap `<script setup>` use nahi kar rahe ho, to ensure karo ki `provide()` synchronously `setup()` ke andar call ho:

```js
import { provide } from 'vue'

export default {
  setup() {
    provide(/* key */ 'message', /* value */ 'hello!')
  }
}
```

`provide()` function do arguments leta hai. Pehla argument hota hai **injection key**, jo ek string ya `Symbol` ho sakta hai. Descendant components isi injection key se desired value ko lookup karte hain. Ek hi component multiple `provide()` calls kar sakta hai alag-alag keys ke saath alag-alag values dene ke liye.

Dusra argument hota hai provide ki gayi value. Yeh kisi bhi type ka ho sakta hai, including reactive state jaise `refs`:

```js
import { ref, provide } from 'vue'

const count = ref(0)
provide('key', count)
```

Reactive values provide karne se descendant components reactive connection establish kar lete hain provider component ke saath.

</div>

<div class="options-api">

Agar aapko apne component ke descendants ko data provide karna hai, to [`provide`](/api/options-composition#provide) option ka use karo:

```js
export default {
  provide: {
    message: 'hello!'
  }
}
```

`provide` object mein har property ka key use hota hai child components ke liye correct value locate karne ke liye, aur value wahi hoti hai jo inject ki jaati hai.

Agar aapko per-instance state provide karna ho, jaise `data()` ke through declare kiya gaya data, to `provide` function ke form mein likhna padega:

```js{7-12}
export default {
  data() {
    return {
      message: 'hello!'
    }
  },
  provide() {
    // function syntax use karo taaki `this` access ho sake
    return {
      message: this.message
    }
  }
}
```

Lekin dhyan rahe ki yeh injection **reactive nahi hoti**. Hum [injections ko reactive kaise banayein](#working-with-reactivity) woh neeche discuss karenge.

</div>

## App-level Provide {#app-level-provide}

Sirf component level pe hi nahi, hum app level pe bhi provide kar sakte hain:

```js
import { createApp } from 'vue'

const app = createApp({})

app.provide(/* key */ 'message', /* value */ 'hello!')
```

App-level provides har component ke liye available hoti hain jo app mein render ho rahe hain. Yeh especially tab useful hota hai jab hum [plugins](/guide/reusability/plugins) likh rahe hote hain, kyunki plugins generally components ke through values provide nahi kar paate.

## Inject {#inject}

<div class="composition-api">

Agar aapko kisi ancestor component se provided data ko use karna hai, to [`inject()`](/api/composition-api-dependency-injection#inject) function ka use karo:

```vue
<script setup>
import { inject } from 'vue'

const message = inject('message')
</script>
```

Agar multiple parents same key ke saath data provide kar rahe hain, to `inject` sabse kareeb parent ke value ko resolve karega component ke parent chain mein.

Agar provided value ek ref hai, to woh as-is inject hogi aur **automatically unwrap nahi hogi**. Isse injector component provider ke saath reactive connection maintain kar sakta hai.

[Full provide + inject Example with Reactivity](https://play.vuejs.org/#eNqFUUFugzAQ/MrKF1IpxfeIVKp66Kk/8MWFDXYFtmUbpArx967BhURRU9/WOzO7MzuxV+fKcUB2YlWovXYRAsbBvQije2d9hAk8Xo7gvB11gzDDxdseCuIUG+ZN6a7JjZIvVRIlgDCcw+d3pmvTglz1okJ499I0C3qB1dJQT9YRooVaSdNiACWdQ5OICj2WwtTWhAg9hiBbhHNSOxQKu84WT8LkNQ9FBhTHXyg1K75aJHNUROxdJyNSBVBp44YI43NvG+zOgmWWYGt7dcipqPhGZEe2ef07wN3lltD+lWN6tNkV/37+rdKjK2rzhRTt7f3u41xhe37/xJZGAL2PLECXa9NKdD/a6QTTtGnP88LgiXJtYv4BaLHhvg==)

Dubara dhyan do, agar aap `<script setup>` use nahi kar rahe ho, to `inject()` ko sirf synchronously `setup()` ke andar call karna chahiye:

```js
import { inject } from 'vue'

export default {
  setup() {
    const message = inject('message')
    return { message }
  }
}
```

</div>

<div class="options-api">

Agar aapko ancestor component se provided data inject karna hai, to [`inject`](/api/options-composition#inject) option ka use karo:

```js
export default {
  inject: ['message'],
  created() {
    console.log(this.message) // injected value
  }
}
```

Injections component ke apne state se **pehle** resolve hoti hain, isliye aap injected properties ko `data()` ke andar use kar sakte ho:

```js
export default {
  inject: ['message'],
  data() {
    return {
      // injected value ke basis pe initial data
      fullMessage: this.message
    }
  }
}
```

Agar multiple parents same key ke saath data provide karte hain, to inject sabse kareeb parent ke value ko resolve karega component ke parent chain mein.

[Full provide + inject example](https://play.vuejs.org/#eNqNkcFqwzAQRH9l0EUthOhuRKH00FO/oO7B2JtERZaEvA4F43+vZCdOTAIJCImRdpi32kG8h7A99iQKobs6msBvpTNt8JHxcTC2wS76FnKrJpVLZelKR39TSUO7qreMoXRA7ZPPkeOuwHByj5v8EqI/moZeXudCIBL30Z0V0FLXVXsqIA9krU8R+XbMR9rS0mqhS4KpDbZiSgrQc5JKQqvlRWzEQnyvuc9YuWbd4eXq+TZn0IvzOeKr8FvsNcaK/R6Ocb9Uc4FvefpE+fMwP0wH8DU7wB77nIo6x6a2hvNEME5D0CpbrjnHf+8excI=)

### Injection Aliasing \* {#injection-aliasing}

Jab aap `inject` ke liye array syntax use karte ho, to injected properties component instance pe usi key ke naam se expose hoti hain. Upar wale example mein property `"message"` ke naam se provide ki gayi thi, aur `this.message` ke roop mein inject ki gayi. Local key aur injection key same hoti hain.

Agar aap local key ko alag naam dena chahte ho, to `inject` option ka object syntax use karo:

```js
export default {
  inject: {
    /* local key */ localMessage: {
      from: /* injection key */ 'message'
    }
  }
}
```

Yahaan component `"message"` key se provided value ko locate karega, aur use `this.localMessage` ke naam se expose karega.

</div>

### Injection Default Values {#injection-default-values}

By default, `inject` maan ke chalta hai ki injected key parent chain mein kahin provide ki gayi hai. Agar key provide nahi ki gayi to runtime warning aayega.

Agar aapko injected property ko optional banana hai, to aap default value declare kar sakte ho, bilkul props ke tarah:

<div class="composition-api">

```js
// Agar "message" provide nahi kiya gaya to `value` banega "default value"
const value = inject('message', 'default value')
```

Kabhi-kabhi default value ko create karne ke liye function ya naye class ka instance banana padta hai. Aise cases mein, jahan value ka use na bhi ho to unnecessary computation ya side effects se bacha jaaye, aap factory function ka use kar sakte ho:

```js
const value = inject('key', () => new ExpensiveClass(), true)
```

Teesra parameter batata hai ki default value ko factory function ki tarah treat kiya jaaye.

</div>

<div class="options-api">

```js
export default {
  // object syntax is required
  // when declaring default values for injections
  inject: {
    message: {
      from: 'message', // this is optional if using the same key for injection
      default: 'default value'
    },
    user: {
      // use a factory function for non-primitive values that are expensive
      // to create, or ones that should be unique per component instance.
      default: () => ({ name: 'John' })
    }
  }
}
```

</div>

## Working with Reactivity {#working-with-reactivity}

<div class="composition-api">

Jab aap reactive provide / inject values ka use kar rahe ho, to **recommended yeh hai ki jitni bhi state mutations hain, wo provider component ke andar hi handle ki jayein**. Isse maintain karna easy hota hai future mein, kyunki state aur uski updates ek hi jagah pe defined hoti hain.

Kuch situations mein injector component se bhi data update karna pad sakta hai. Aise cases mein, ek function provide karna sahi rahega jo state ko mutate kare:

```vue{7-9,13}
<!-- provider component ke andar -->
<script setup>
import { provide, ref } from 'vue'

const location = ref('North Pole')

function updateLocation() {
  location.value = 'South Pole'
}

provide('location', {
  location,
  updateLocation
})
</script>
```

```vue{5}
<!-- injector component ke andar -->
<script setup>
import { inject } from 'vue'

const { location, updateLocation } = inject('location')
</script>

<template>
  <button @click="updateLocation">{{ location }}</button>
</template>
```

Agar aap chaahte ho ki injector component provided data ko mutate na kar sake, to aap [`readonly()`](/api/reactivity-core#readonly) ka use karke value ko readonly bana sakte ho:

```vue
<script setup>
import { ref, provide, readonly } from 'vue'

const count = ref(0)
provide('read-only-count', readonly(count))
</script>
```

</div>

<div class="options-api">

Agar aap chahte ho ki injection reactive ho provider ke saath, to aapko [computed()](/api/reactivity-core#computed) function ka use karke computed property provide karni padegi:

```js{10}
import { computed } from 'vue'

export default {
  data() {
    return {
      message: 'hello!'
    }
  },
  provide() {
    return {
      // explicitly ek computed property provide kar rahe hain
      message: computed(() => this.message)
    }
  }
}
```

[Full provide + inject Example with Reactivity](https://play.vuejs.org/#eNqNUctqwzAQ/JVFFyeQxnfjBEoPPfULqh6EtYlV9EKWTcH43ytZtmPTQA0CsdqZ2dlRT16tPXctkoKUTeWE9VeqhbLGeXirheRwc0ZBds7HKkKzBdBDZZRtPXIYJlzqU40/I4LjjbUyIKmGEWw0at8UgZrUh1PscObZ4ZhQAA596/RcAShsGnbHArIapTRBP74O8Up060wnOO5QmP0eAvZyBV+L5jw1j2tZqsMp8yWRUHhUVjKPoQIohQ460L0ow1FeKJlEKEnttFweijJfiORElhCf5f3umObb0B9PU/I7kk17PJj7FloN/2t7a2Pj/Zkdob+x8gV8ZlMs2de/8+14AXwkBngD9zgVqjg2rNXPvwjD+EdlHilrn8MvtvD1+Q==)

`computed()` function mainly Composition API mein use hota hai, lekin kuch Options API ke cases mein bhi useful ho sakta hai. Iske use ko deeply samajhne ke liye [Reactivity Fundamentals](/guide/essentials/reactivity-fundamentals) aur [Computed Properties](/guide/essentials/computed) padh sakte ho, API Preference ko Composition API pe set karke.

</div>

## Working with Symbol Keys {#working-with-symbol-keys}

Ab tak hum examples mein string injection keys ka use kar rahe the. Lekin agar aap ek large application mein kaam kar rahe ho jahan multiple dependency providers hain, ya phir aap aise components bana rahe ho jo doosre developers bhi use karenge, to **Symbol injection keys ka use karna best practice hota hai**, taki key collisions avoid ho sakein.

Recommend kiya jaata hai ki Symbols ko ek dedicated file mein export karo.

```js
// keys.js
export const myInjectionKey = Symbol()
```

<div class="composition-api">

```js
// in provider component
import { provide } from 'vue'
import { myInjectionKey } from './keys.js'

provide(myInjectionKey, {
  /* data to provide */
})
```

```js
// in injector component
import { inject } from 'vue'
import { myInjectionKey } from './keys.js'

const injected = inject(myInjectionKey)
```

See also: [Typing Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

</div>

<div class="options-api">

```js
// in provider component
import { myInjectionKey } from './keys.js'

export default {
  provide() {
    return {
      [myInjectionKey]: {
        /* data to provide */
      }
    }
  }
}
```

```js
// in injector component
import { myInjectionKey } from './keys.js'

export default {
  inject: {
    injected: { from: myInjectionKey }
  }
}
```

</div>
