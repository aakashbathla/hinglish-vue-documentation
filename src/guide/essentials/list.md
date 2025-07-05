# List Rendering {#list-rendering}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/list-rendering-in-vue-3" title="Free Vue.js List Rendering Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-list-rendering-in-vue" title="Free Vue.js List Rendering Lesson"/>
</div>

## `v-for` {#v-for}

Hum `v-for` directive ka use ek array ke base par list of items render karne ke liye kar sakte hain. `v-for` directive ek special syntax maangta hai `item in items` ke form mein, jahan `items` ek source data array hai aur `item` us array ke element ka **alias** hai jisko loop mein iterate kiya ja raha hai:

<div class="composition-api">

```js
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>

<div class="options-api">

```js
data() {
  return {
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html
<li v-for="item in items">
  {{ item.message }}
</li>
```

`v-for` ke scope ke andar, template expressions ko parent scope ke saare properties ka access hota hai. Iske alawa, `v-for` ek optional second alias bhi support karta hai jo current item ka index hota hai:

<div class="composition-api">

```js
const parentMessage = ref('Parent')
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

</div>
<div class="options-api">

```js
data() {
  return {
    parentMessage: 'Parent',
    items: [{ message: 'Foo' }, { message: 'Bar' }]
  }
}
```

</div>

```vue-html
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```

<script setup>
const parentMessage = 'Parent'
const items = [{ message: 'Foo' }, { message: 'Bar' }]
</script>
<div class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</div>

<div class="composition-api">

[Playground mein try karein](https://play.vuejs.org/#eNpdTsuqwjAQ/ZVDNlFQu5d64bpwJ7g3LopOJdAmIRlFCPl3p60PcDWcM+eV1X8Iq/uN1FrV6RxtYCTiW/gzzvbBR0ZGpBYFbfQ9tEi1ccadvUuM0ERyvKeUmithMyhn+jCSev4WWaY+vZ7HjH5Sr6F33muUhTR8uW0ThTuJua6mPbJEgGSErmEaENedxX3Z+rgxajbEL2DdhR5zOVOdUSIEDOf8M7IULCHsaPgiMa1eK4QcS6rOSkhdfapVeQLQEWnH)

</div>
<div class="options-api">

[Playground mein try karein](https://play.vuejs.org/#eNpVTssKwjAQ/JUllyr0cS9V0IM3wbvxEOxWAm0a0m0phPy7m1aqhpDsDLMz48XJ2nwaUZSiGp5OWzpKg7PtHUGNjRpbAi8NQK1I7fbrLMkhjc5EJAn4WOXQ0BWHQb2whOS24CSN6qjXhN1Qwt1Dt2kufZ9ASOGXOyvH3GMNCdGdH75VsZVjwGa2VYQRUdVqmLKmdwcpdjEnBW1qnPf8wZIrBQujoff/RSEEyIDZZeGLeCn/dGJyCSlazSZVsUWL8AYme21i)

</div>

`v-for` ka variable scoping kuch is JavaScript ke tarike se kaam karta hai:

```js
const parentMessage = 'Parent'
const items = [
  /* ... */
]

items.forEach((item, index) => {
  // outer scope ka `parentMessage` yahan access ho sakta hai
  // lekin `item` aur `index` sirf is block ke andar hi available hain
  console.log(parentMessage, item.message, index)
})
```

Notice karo kaise `v-for` ka value `forEach` callback function ke signature se match karta hai. Asal mein, aap `v-for` item alias par destructuring ka use kar sakte ho, bilkul function arguments ki tarah:

```vue-html
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- with index alias -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

Nested `v-for` ke case mein bhi scoping nested functions jaise kaam karta hai. Har `v-for` scope ko parent scopes ka access hota hai:

```vue-html
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

Aap `in` ki jagah `of` bhi use kar sakte ho delimiter ke roop mein, taaki JavaScript ke iterator syntax ke kareeb ho:

```vue-html
<div v-for="item of items"></div>
```

## `v-for` with an Object {#v-for-with-an-object}

Aap `v-for` ka use ek object ke properties ko iterate karne ke liye bhi kar sakte ho. Iteration order `Object.values()` ke result par based hoti hai:

<div class="composition-api">

```js
const myObject = reactive({
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    myObject: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
}
```

</div>

```vue-html
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

Aap second alias bhi de sakte ho property name (yaani key) ke liye:

```vue-html
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```

Aur ek aur alias index ke liye:

```vue-html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

<div class="composition-api">

[Playground mein try karein](https://play.vuejs.org/#eNo9jjFvgzAQhf/KE0sSCQKpqg7IqRSpQ9WlWycvBC6KW2NbcKaNEP+9B7Tx4nt33917Y3IKYT9ESspE9XVnAqMnjuFZO9MG3zFGdFTVbAbChEvnW2yE32inXe1dz2hv7+dPqhnHO7kdtQPYsKUSm1f/DfZoPKzpuYdx+JAL6cxUka++E+itcoQX/9cO8SzslZoTy+yhODxlxWN2KMR22mmn8jWrpBTB1AZbMc2KVbTyQ56yBkN28d1RJ9uhspFSfNEtFf+GfnZzjP/oOll2NQPjuM4xTftZyIaU5VwuN0SsqMqtWZxUvliq/J4jmX4BTCp08A==)

</div>
<div class="options-api">

[Playground mein try karein](https://play.vuejs.org/#eNo9T8FqwzAM/RWRS1pImnSMHYI3KOwwdtltJ1/cRqXe3Ng4ctYS8u+TbVJjLD3rPelpLg7O7aaARVeI8eS1ozc54M1ZT9DjWQVDMMsBoFekNtucS/JIwQ8RSQI+1/vX8QdP1K2E+EmaDHZQftg/IAu9BaNHGkEP8B2wrFYxgAp0sZ6pn2pAeLepmEuSXDiy7oL9gduXT+3+pW6f631bZoqkJY/kkB6+onnswoDw6owijIhEMByjUBgNU322/lUWm0mZgBX84r1ifz3ettHmupYskjbanedch2XZRcAKTnnvGVIPBpkqGqPTJNGkkaJ5+CiWf4KkfBs=)

</div>

## `v-for` with a Range {#v-for-with-a-range}

`v-for` ek integer bhi le sakta hai. Is case mein ye template ko utni baar repeat karega, `1...n` range ke base par.

```vue-html
<span v-for="n in 10">{{ n }}</span>
```

Note karein `n` ki starting value `1` hoti hai, `0` nahi.

## `v-for` on `<template>` {#v-for-on-template}

Template `v-if` ki tarah, aap `<template>` tag ke saath `v-for` ka use karke ek block of multiple elements render kar sakte ho. Jaise:

```vue-html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` with `v-if` {#v-for-with-v-if}

Jab ye dono same node par hote hain, `v-if` ki priority `v-for` se zyada hoti hai. Iska matlab hai ki `v-if` condition ko `v-for` ke scope wale variables ka access nahi milega:

```vue-html
<!--
Yeh error dega kyunki "todo" property instance par define nahi hai.
-->
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

Isse fix karne ke liye `v-for` ko ek wrapping `<template>` tag par shift kar do (jo ki aur bhi zyada clear hota hai):

```vue-html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

:::warning Note
Same element par `v-if` aur `v-for` ka use karna recommend nahi hai, kyunki inki precedence implicit hoti hai.

Do common cases jahan aapko ye tempting lag sakta hai:

- List mein items ko filter karna (jaise `v-for="user in users" v-if="user.isActive"`). Aise cases mein, `users` ko ek nayi computed property se replace karo jo filtered list return kare (jaise `activeUsers`).

- List ko tab render na karna jab wo hidden honi chahiye (jaise `v-for="user in users" v-if="shouldShowUsers"`). Aise cases mein, `v-if` ko ek container element (jaise `ul`, `ol`) par move karo.
  :::

## Maintaining State with `key` {#maintaining-state-with-key}

Jab Vue ek `v-for` se render hui list ko update karta hai, toh by default wo "in-place patch" strategy use karta hai. Agar data items ka order change ho gaya ho, toh DOM elements ko move karne ke bajaye, Vue har element ko usi jagah update karta hai taaki wo naye index ka data dikhaye.

Ye default mode efficient hota hai, lekin **sirf tab theek hai jab aapka list render output kisi child component ke state ya temporary DOM state (jaise form inputs) par depend nahi karta ho.**

Vue ko hint dene ke liye taaki wo har node ki identity ko track kar sake aur existing elements ko reuse aur reorder kar sake, aapko har item ke liye ek unique `key` attribute dena hota hai:

```vue-html
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

Agar aap `<template v-for>` use kar rahe ho, toh `key` ko `<template>` container par lagana chahiye:

```vue-html
<template v-for="todo in todos" :key="todo.name">
  <li>{{ todo.name }}</li>
</template>
```

:::tip Note
Yahan `key` ek special attribute hai jo `v-bind` ke saath bind ho raha hai. Isse confuse na karein uss property key variable ke saath jo [object ke saath `v-for`](#v-for-with-an-object) use karte waqt milta hai.
:::

`v-for` ke saath `key` dena recommended hai jab bhi possible ho, agar aapka DOM content simple nahi hai (yaani usme components ya stateful elements hain), ya aap jaan bujh kar default behavior ka fayda lena chahte hain performance ke liye.

`key` primitive values (jaise strings ya numbers) expect karta hai. Objects ko `v-for` keys ke roop mein mat use karo. `key` attribute ke detailed usage ke liye dekhein: [`key` API documentation](/api/built-in-special-attributes#key).

## `v-for` with a Component {#v-for-with-a-component}

> Ye section assume karta hai ki aap [Components](/guide/essentials/component-basics) ke baare mein jaante hain. Agar nahi, toh isse skip kar dein aur baad mein padhein.

Aap component par bhi directly `v-for` use kar sakte ho, jaise kisi normal element par (bas `key` dena na bhoolen):

```vue-html
<MyComponent v-for="item in items" :key="item.id" />
```

Lekin ye automatically component ko koi data pass nahi karega, kyunki components ka apna isolated scope hota hai. Iterated data ko component mein bhejne ke liye aapko props ka use karna chahiye:

```vue-html
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```

The reason for not automatically injecting `item` into the component is because that makes the component tightly coupled to how `v-for` works. Being explicit about where its data comes from makes the component reusable in other situations.

<div class="composition-api">

[Is simple todo list ka example dekhein](https://play.vuejs.org/#eNp1U8Fu2zAM/RXCGGAHTWx02ylwgxZYB+ywYRhyq3dwLGYRYkuCJTsZjPz7KMmK3ay9JBQfH/meKA/Rk1Jp32G0jnJdtVwZ0Gg6tSkEb5RsDQzQ4h4usG9lAzGVxldoK5n8ZrAZsTQLCduRygAKUUmhDQg8WWyLZwMPtmESx4sAGkL0mH6xrMH+AHC2hvuljw03Na4h/iLBHBAY1wfUbsTFVcwoH28o2/KIIDuaQ0TTlvrwNu/TDe+7PDlKXZ6EZxTiN4kuRI3W0dk4u4yUf7bZfScqw6WAkrEf3m+y8AOcw7Qv6w5T1elDMhs7Nbq7e61gdmme60SQAvgfIhExiSSJeeb3SBukAy1D1aVBezL5XrYN9Csp1rrbNdykqsUehXkookl0EVGxlZHX5Q5rIBLhNHFlbRD6xBiUzlOeuZJQz4XqjI+BxjSSYe2pQWwRBZizV01DmsRWeJA1Qzv0Of2TwldE5hZRlVd+FkbuOmOksJLybIwtkmfWqg+7qz47asXpSiaN3lxikSVwwfC8oD+/sEnV+oh/qcxmU85mebepgLjDBD622Mg+oDrVquYVJm7IEu4XoXKTZ1dho3gnmdJhedEymn9ab3ysDPdc4M9WKp28xE5JbB+rzz/Trm3eK3LAu8/E7p2PNzYM/i3ChR7W7L7hsSIvR7L2Aal1EhqTp80vF95sw3WcG7r8A0XaeME=) jisme `v-for` ke saath components ko render karke unhe alag data pass kiya gaya hai.

</div>
<div class="options-api">

[Is simple todo list ka example dekhein](https://play.vuejs.org/#eNqNVE2PmzAQ/SsjVIlEm4C27Qmx0a7UVuqhPVS5lT04eFKsgG2BSVJF+e8d2xhIu10tihR75s2bNx9wiZ60To49RlmUd2UrtNkUUjRatQa2iquvBhvYt6qBOEmDwQbEhQQoJJ4dlOOe9bWBi7WWiuIlStNlcJlYrivr5MywxdIDAVo0fSvDDUDiyeK3eDYZxLGLsI8hI7H9DHeYQuwjeAb3I9gFCFMjUXxSYCoELroKO6fZP17Mf6jev0i1ZQcE1RtHaFrWVW/l+/Ai3zd1clQ1O8k5Uzg+j1HUZePaSFwfvdGhfNIGTaW47bV3Mc6/+zZOfaaslegS18ZE9121mIm0Ep17ynN3N5M8CB4g44AC4Lq8yTFDwAPNcK63kPTL03HR6EKboWtm0N5MvldtA8e1klnX7xphEt3ikTbpoYimsoqIwJY0r9kOa6Ag8lPeta2PvE+cA3M7k6cOEvBC6n7UfVw3imPtQ8eiouAW/IY0mElsiZWqOdqkn5NfCXxB5G6SJRvj05By1xujpJWUp8PZevLUluqP/ajPploLasmk0Re3sJ4VCMnxvKQ//0JMqrID/iaYtSaCz+xudsHjLpPzscVGHYO3SzpdixIXLskK7pcBucnTUdgg3kkmcxhetIrmH4ebr8m/n4jC6FZp+z7HTlLsVx1p4M7odcXPr6+Lnb8YOne5+C2F6/D6DH2Hx5JqOlCJ7yz7IlBTbZsf7vjXVBzjvLDrH5T0lgo=) jisme `v-for` ke saath components ko render karke unhe alag data pass kiya gaya hai.

</div>

## Array Change Detection {#array-change-detection}

### Mutation Methods {#mutation-methods}

Vue reactive array ke mutation methods ko detect kar leta hai aur zaroori updates trigger karta hai. Ye mutation methods hain:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

### Replacing an Array {#replacing-an-array}

Mutation methods original array ko hi mutate karte hain. Iske comparison mein kuch non-mutating methods hote hain, jaise `filter()`, `concat()` aur `slice()`, jo original array ko mutate nahi karte, balki **hamesha ek naya array return karte hain**. Jab aap non-mutating methods ke saath kaam kar rahe ho, toh purane array ko naye se replace karna chahiye:

<div class="composition-api">

```js
// `items` ek ref hai jisme array value hai
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

</div>
<div class="options-api">

```js
this.items = this.items.filter((item) => item.message.match(/Foo/))
```

</div>

Ho sakta hai aap sochein ki Vue isse pura DOM hata ke dobara render karega — lekin aisa nahi hai. Vue kuch smart techniques use karta hai taaki DOM elements ka reuse maximize ho sake, isliye overlapping objects ke saath array replace karna kaafi efficient operation hai.

## Displaying Filtered/Sorted Results {#displaying-filtered-sorted-results}

Kabhi-kabhi hume kisi array ka filtered ya sorted version show karna hota hai bina original data ko mutate ya reset kiye. Aise mein aap ek computed property bana sakte ho jo filtered ya sorted array return kare.

Example:

<div class="composition-api">

```js
const numbers = ref([1, 2, 3, 4, 5])

const evenNumbers = computed(() => {
  return numbers.value.filter((n) => n % 2 === 0)
})
```

</div>
<div class="options-api">

```js
data() {
  return {
    numbers: [1, 2, 3, 4, 5]
  }
},
computed: {
  evenNumbers() {
    return this.numbers.filter(n => n % 2 === 0)
  }
}
```

</div>

```vue-html
<li v-for="n in evenNumbers">{{ n }}</li>
```

Aise situations mein jahan computed property feasible nahi hai (jaise nested `v-for` loops ke andar), aap method use kar sakte ho:

<div class="composition-api">

```js
const sets = ref([
  [1, 2, 3, 4, 5],
  [6, 7, 8, 9, 10]
])

function even(numbers) {
  return numbers.filter((number) => number % 2 === 0)
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
  }
},
methods: {
  even(numbers) {
    return numbers.filter(number => number % 2 === 0)
  }
}
```

</div>

```vue-html
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
```

`reverse()` aur `sort()` ka use computed property mein karte waqt dhyan rakhein! Ye methods original array ko mutate karte hain, jo computed getter mein avoid karna chahiye. Inse pehle array ki ek copy bana lo:

```diff
- return numbers.reverse()
+ return [...numbers].reverse()
```
