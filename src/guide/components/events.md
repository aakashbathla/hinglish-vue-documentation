<script setup>
import { onMounted } from 'vue'

if (typeof window !== 'undefined') {
  const hash = window.location.hash

  // Pehle v-model ka documentation isi page mein tha. Purane links ko redirect karne ki koshish.
  if ([
    '#usage-with-v-model',
    '#v-model-arguments',
    '#multiple-v-model-bindings',
    '#handling-v-model-modifiers'
  ].includes(hash)) {
    onMounted(() => {
      window.location = './v-model.html' + hash
    })
  }
}
</script>

# Component Events {#component-events}

> Ye page maan ke chalta hai ki aap pehle hi [Components Basics](/guide/essentials/component-basics) padh chuke ho. Agar aap components mein naye ho toh pehle usse padh lena.

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/defining-custom-events-emits" title="Free Vue.js Lesson on Defining Custom Events"/>
</div>

## Emitting and Listening to Events {#emitting-and-listening-to-events}

Ek component directly apne template expressions (jaise `v-on` handler) mein `$emit` method ka use karke custom events emit kar sakta hai:

```vue-html
<!-- MyComponent -->
<button @click="$emit('someEvent')">Click Me</button>
```

<div class="options-api">

`$emit()` method component instance par `this.$emit()` ke roop mein bhi available hota hai:

```js
export default {
  methods: {
    submit() {
      this.$emit('someEvent')
    }
  }
}
```

</div>

Parent component isse `v-on` ke through sun sakta hai:

```vue-html
<MyComponent @some-event="callback" />
```

`.once` modifier bhi component event listeners par kaam karta hai:

```vue-html
<MyComponent @some-event.once="callback" />
```

Jaise components aur props ke saath hota hai, waise hi event names mein bhi automatic case transformation hoti hai. Dhyan do ki humne camelCase event emit kiya, lekin parent mein kebab-case listener use kiya. Jaise [props casing](/guide/components/props#prop-name-casing) mein suggest kiya gaya hai, templates mein kebab-case event listeners use karna recommended hai.

:::tip
Native DOM events ke unlike, component-emitted events **bubble nahi karte**. Aap sirf direct child component ke emit kiye gaye events ko sun sakte ho. Agar sibling ya deeply nested components ke beech communication chahiye, toh external event bus ya [global state management solution](/guide/scaling-up/state-management) ka use karo.
:::

## Event Arguments {#event-arguments}

Kabhi-kabhi kisi specific value ko event ke saath emit karna useful hota hai. Jaise ki, hum chahte hain ki `<BlogPost>` component decide kare ki text kitna bada karna hai. Aise cases mein hum `$emit` ko extra arguments ke saath call kar sakte hain:

```vue-html
<button @click="$emit('increaseBy', 1)">
  Increase by 1
</button>
```

Phir, jab parent component mein event ko sunte hain, toh inline arrow function listener ke roop mein use karke hum argument ko access kar sakte hain:

```vue-html
<MyButton @increase-by="(n) => count += n" />
```

Ya agar event handler ek method hai:

```vue-html
<MyButton @increase-by="increaseCount" />
```

Toh value us method ka pehla parameter ban jaayegi:

<div class="options-api">

```js
methods: {
  increaseCount(n) {
    this.count += n
  }
}
```

</div>
<div class="composition-api">

```js
function increaseCount(n) {
  count.value += n
}
```

</div>

:::tip
Event name ke baad `$emit()` mein jitne bhi extra arguments diye jaate hain, wo sab listener function tak forward ho jaate hain. Jaise ki, `$emit('foo', 1, 2, 3)` mein listener ko teen arguments milenge.
:::

## Declaring Emitted Events {#declaring-emitted-events}

Ek component explicitly batata hai ki wo kaunse events emit karega, ye declare kiya ja sakta hai <span class="composition-api">[`defineEmits()`](/api/sfc-script-setup#defineprops-defineemits) macro</span> ya <span class="options-api">[`emits`](/api/options-state#emits) option</span> ke through:

<div class="composition-api">

```vue
<script setup>
defineEmits(['inFocus', 'submit'])
</script>
```

Jo `$emit` method humne `<template>` mein use kiya tha, wo `<script setup>` ke andar directly accessible nahi hota. Lekin `defineEmits()` ek equivalent function return karta hai jo hum use kar sakte hain:

```vue
<script setup>
const emit = defineEmits(['inFocus', 'submit'])

function buttonClick() {
  emit('submit')
}
</script>
```

`defineEmits()` macro ko kisi function ke andar use nahi kiya ja sakta, use seedha `<script setup>` ke andar likhna hota hai.

Agar aap `<script setup>` ke bajay explicit `setup` function use kar rahe ho, toh events ko [`emits`](/api/options-state#emits) option ke through declare kiya jaata hai, aur `emit` function `setup()` ke context mein available hota hai:

```js
export default {
  emits: ['inFocus', 'submit'],
  setup(props, ctx) {
    ctx.emit('submit')
  }
}
```

Baaki `setup()` context properties ki tarah, `emit` ko destructure bhi kiya ja sakta hai:

```js
export default {
  emits: ['inFocus', 'submit'],
  setup(props, { emit }) {
    emit('submit')
  }
}
```

</div>
<div class="options-api">

```js
export default {
  emits: ['inFocus', 'submit']
}
```

</div>

`emits` option aur `defineEmits()` macro object syntax bhi support karte hain. Agar aap TypeScript use kar rahe ho toh aap arguments ko type bhi kar sakte ho, jisse emitted events ke payload ka runtime validation possible hota hai:

<div class="composition-api">

```vue
<script setup lang="ts">
const emit = defineEmits({
  submit(payload: { email: string; password: string }) {
    // true ya false return karo batane ke liye
    // validation pass hua ya nahi
  }
})
</script>
```

Agar aap TypeScript ke saath `<script setup>` use kar rahe ho, toh aap type annotations ke through events declare bhi kar sakte ho:

```vue
<script setup lang="ts">
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()
</script>
```

Zyada details: [Typing Component Emits](/guide/typescript/composition-api#typing-component-emits) <sup class="vt-badge ts" />

</div>
<div class="options-api">

```js
export default {
  emits: {
    submit(payload: { email: string, password: string }) {
      // true ya false return karo batane ke liye
      // validation pass hua ya nahi
    }
  }
}
```

Dekho bhi: [Typing Component Emits](/guide/typescript/options-api#typing-component-emits) <sup class="vt-badge ts" />

</div>

Yeh optional hai, lekin recommended hai ki aap har emit hone wale event ko define karo, taaki component ka usage achhi tarah document ho sake. Isse Vue ko known listeners ko [fallthrough attributes](/guide/components/attrs#v-on-listener-inheritance) se alag karne mein bhi help milti hai, aur third-party code ke manual DOM events se hone wale edge cases se bachav hota hai.

:::tip
Agar koi native event (jaise `click`) `emits` option mein define kiya gaya hai, toh listener sirf component-emitted `click` events ko sunega, native `click` events ko nahi.
:::

## Events Validation {#events-validation}

Props ke tarah, emitted event ko bhi validate kiya ja sakta hai agar wo array syntax ke bajay object syntax mein define kiya gaya ho.

Validation ke liye, event ko ek function assign kiya jaata hai jo <span class="options-api">`this.$emit`</span><span class="composition-api">`emit`</span> call ke arguments ko leta hai aur batata hai ki event valid hai ya nahi (boolean return karta hai).

<div class="composition-api">

```vue
<script setup>
const emit = defineEmits({
  // koi validation nahi
  click: null,

  // submit event ko validate karo
  submit: ({ email, password }) => {
    if (email && password) {
      return true
    } else {
      console.warn('Invalid submit event payload!')
      return false
    }
  }
})

function submitForm(email, password) {
  emit('submit', { email, password })
}
</script>
```

</div>
<div class="options-api">

```js
export default {
  emits: {
    // koi validation nahi
    click: null,

    // submit event ko validate karo
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm(email, password) {
      this.$emit('submit', { email, password })
    }
  }
}
```

</div>
