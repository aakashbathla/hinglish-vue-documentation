# Props {#props}

> Is page ka assume hai ki aapne already [Components Basics](/guide/essentials/component-basics) padh liya hai. Agar aap components mein naye ho, toh pehle woh padh lo.

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-3-reusable-components-with-props" title="Free Vue.js Props Lesson"/>
</div>

## Props Declaration {#props-declaration}

Vue components mein explicitly props declare karne padte hain taaki Vue samajh sake ki kaunse external props ko fallthrough attributes ke roop mein treat karna hai (jo ki [is section](/guide/components/attrs) mein discuss kiya gaya hai).

<div class="composition-api">

Agar aap SFCs mein `<script setup>` use kar rahe ho, toh props ko `defineProps()` macro se declare kar sakte ho:

```vue
<script setup>
const props = defineProps(['foo'])

console.log(props.foo)
</script>
```

Agar aap non-`<script setup>` component use kar rahe ho, toh props ko [`props`](/api/options-state#props) option se declare kiya jaata hai:

```js
export default {
  props: ['foo'],
  setup(props) {
    // setup() function ko props first argument ke roop mein milte hain
    console.log(props.foo)
  }
}
```

Dhyan do ki `defineProps()` mein jo argument diya gaya hai, wohi `props` option mein diya gaya value hota hai: dono declaration styles same props API share karte hain.

</div>

<div class="options-api">

Props ko declare karne ke liye [`props`](/api/options-state#props) option use hota hai:

```js
export default {
  props: ['foo'],
  created() {
    // props `this` par available hote hain
    console.log(this.foo)
  }
}
```

</div>

Strings ki array ke alawa aap object syntax ka bhi use kar sakte ho props declare karne ke liye:

<div class="options-api">

```js
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

</div>
<div class="composition-api">

```js
// agar aap <script setup> use kar rahe ho
defineProps({
  title: String,
  likes: Number
})
```

```js
// agar aap non-<script setup> use kar rahe ho
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

</div>

Object syntax mein har property ka key hota hai prop ka naam, aur value hota hai expected type ka constructor function.

Yeh na sirf aapke component ko document karta hai, balki agar koi galat type ka prop pass karega toh browser console mein warning bhi dikhayi jaayegi. Hum [prop validation](#prop-validation) ke baare mein aur detail mein neeche discuss karenge.

<div class="options-api">

See also: [Typing Component Props](/guide/typescript/options-api#typing-component-props) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

Agar aap TypeScript ke saath `<script setup>` use kar rahe ho, toh aap pure type annotations se bhi props declare kar sakte ho:

```vue
<script setup lang="ts">
defineProps<{
  title?: string
  likes?: number
}>()
</script>
```

More details: [Typing Component Props](/guide/typescript/composition-api#typing-component-props) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

## Reactive Props Destructure <sup class="vt-badge" data-text="3.5+" /> \*\* {#reactive-props-destructure}

Vue ka reactivity system state usage ko track karta hai based on property access. Matlab agar aap `props.foo` ko kisi computed getter ya watcher mein access karte ho, toh `foo` prop ek dependency ke roop mein track ho jaata hai.

Toh maan lo aapne yeh code likha:

```js
const { foo } = defineProps(['foo'])

watchEffect(() => {
  // Vue 3.5 se pehle sirf ek baar chalega
  // 3.5+ mein "foo" prop change hote hi dubara chalega
  console.log(foo)
})
```

Version 3.4 ya usse pehle mein, `foo` ek constant hota hai jo kabhi change nahi hota. Lekin version 3.5 se Vue ka compiler automatically `props.` add kar deta hai jab `<script setup>` block ke andar destructured variable ko access kiya jaata hai. Isliye upar wala code compiler ke liye kuch aise ban jaata hai:

```js {5}
const props = defineProps(['foo'])

watchEffect(() => {
  // `foo` transformed to `props.foo` by the compiler
  console.log(props.foo)
})
```

Aap JavaScript ka native default value syntax bhi use kar sakte ho props ke liye default values declare karne ke liye. Yeh especially tab useful hota hai jab aap type-based props declaration use kar rahe ho:

```ts
const { foo = 'hello' } = defineProps<{ foo?: string }>()
```

Agar aapko apne IDE mein destructured props aur normal variables ke beech visual difference chahiye, toh Vue ka VSCode extension ek setting provide karta hai jisse aap inlay-hints enable kar sakte ho.

### Passing Destructured Props into Functions {#passing-destructured-props-into-functions}

Jab hum ek destructured prop ko function mein pass karte hain, jaise ki:

```js
const { foo } = defineProps(['foo'])

watch(foo /* ... */)
```

Toh yeh expected tarike se kaam nahi karega, kyunki yeh `watch(props.foo, ...)` ke barabar hota hai — hum yahan reactive source ki jagah ek value pass kar rahe hain. Vue ka compiler aise cases ko pakad lega aur warning dega.

Jaise hum normal prop ko `watch(() => props.foo, ...)` ke through watch karte hain, waise hi destructured prop ko bhi getter mein wrap karke watch kar sakte hain:

```js
watch(() => foo /* ... */)
```

Yeh approach recommended hai jab hume ek destructured prop ko kisi external function mein pass karna ho aur reactivity ko maintain rakhna ho:

```js
useComposable(() => foo)
```

External function jab bhi us prop ke changes track karna chahe, toh woh is getter ko call kar sakta hai (ya use [toValue](/api/reactivity-utilities.html#tovalue) se normalize kar sakta hai), jaise computed ya watcher ke andar.

</div>

## Prop Passing Details {#prop-passing-details}

### Prop Name Casing {#prop-name-casing}

Hum lambaa prop name camelCase mein declare karte hain kyunki isse property keys mein quotes lagane ki zarurat nahi padti, aur template expressions mein directly refer bhi kar sakte hain — kyunki ye valid JavaScript identifiers hote hain.

<div class="composition-api">

```js
defineProps({
  greetingMessage: String
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    greetingMessage: String
  }
}
```

</div>

```vue-html
<span>{{ greetingMessage }}</span>
```

Technically, aap camelCase props ko child component mein pass karte waqt bhi use kar sakte ho (bas in-DOM templates ke alawa). Lekin convention ye hai ki hum hamesha kebab-case ka use karein taaki HTML attributes ke saath alignment bana rahe:

```vue-html
<MyComponent greeting-message="hello" />
```

Hum PascalCase use karte hain component tags ke liye jab possible ho, taaki readability better ho aur native HTML elements se clearly differentiate ho jaye. Lekin props pass karte waqt camelCase use karne ka itna practical benefit nahi hai, isliye hum language-specific conventions follow karte hain.

### Static vs. Dynamic Props {#static-vs-dynamic-props}

Ab tak aapne dekha ki props ko static value ke saath kaise pass kiya jaata hai, jaise:

```vue-html
<BlogPost title="My journey with Vue" />
```

Aur aapne yeh bhi dekha ki props ko dynamically `v-bind` ya `:` shortcut ke through assign kiya ja sakta hai:

```vue-html
<!-- Variable ki value dynamically assign karna -->
<BlogPost :title="post.title" />

<!-- Ek complex expression ki value dynamically assign karna -->
<BlogPost :title="post.title + ' by ' + post.author.name" />
```

### Alag-Alag Value Types Pass Karna {#passing-different-value-types}

Upar ke examples mein humne string values use ki, lekin Vue mein aap kisi bhi type ki value prop ke through bhej sakte ho.

#### Number {#number}

```vue-html
<!-- Chahe `42` static ho, Vue ko batana padta hai ki yeh JS expression hai, string nahi -->
<BlogPost :likes="42" />

<!-- Variable ki value dynamically pass karna -->
<BlogPost :likes="post.likes" />
```

#### Boolean {#boolean}

```vue-html
<!-- Agar prop ko bina value ke likho, toh default `true` maana jaata hai -->
<BlogPost is-published />

<!-- Chahe `false` static ho, v-bind lagana zaroori hai -->
<BlogPost :is-published="false" />

<!-- Variable ki value dynamically pass karna -->
<BlogPost :is-published="post.isPublished" />
```

#### Array {#array}

```vue-html
<!-- Static array ko bhi v-bind se JS expression batana padta hai -->
<BlogPost :comment-ids="[234, 266, 273]" />

<!-- Variable ke through dynamically array pass karna -->
<BlogPost :comment-ids="post.commentIds" />
```

#### Object {#object}

```vue-html
<!-- Static object ke liye bhi v-bind zaroori hai -->
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
/>

<!-- Variable ke through dynamically object pass karna -->
<BlogPost :author="post.author" />
```

### Ek Object ke Multiple Properties ko ek saath Bind Karna {#binding-multiple-properties-using-an-object}

Agar aap ek object ke saare properties ko ek hi baar mein as props pass karna chahte ho, toh aap `v-bind` bina kisi argument ke use kar sakte ho. Jaise agar aapke paas ek `post` object hai:

<div class="options-api">

```js
export default {
  data() {
    return {
      post: {
        id: 1,
        title: 'My Journey with Vue'
      }
    }
  }
}
```

</div>
<div class="composition-api">

```js
const post = {
  id: 1,
  title: 'My Journey with Vue'
}
```

</div>

The following template:

```vue-html
<BlogPost v-bind="post" />
```

Will be equivalent to:

```vue-html
<BlogPost :id="post.id" :title="post.title" />
```

## One-Way Data Flow {#one-way-data-flow}

Saare props ek **one-way-down binding** banate hain child component ke property aur parent ke property ke beech: jab parent property update hoti hai, tab woh value child tak flow karti hai — lekin child se wapas parent tak nahi jaati. Yeh system isliye rakha gaya hai taaki child components galti se parent ka state mutate na kar dein, jo ki aapke app ka data flow samajhne mein dikkat daal sakta hai.

Iske alawa, jab bhi parent component update hota hai, child component ke saare props latest value ke saath refresh ho jaate hain. Iska matlab yeh hai ki aapko child component ke andar kisi bhi prop ko mutate karne ki koshish **nahi** karni chahiye. Agar aap aisa karte ho, toh Vue aapko console mein warning dega.

<div class="composition-api">

```js
const props = defineProps(['foo'])

// ❌ warning, props are readonly!
props.foo = 'bar'
```

</div>
<div class="options-api">

```js
export default {
  props: ['foo'],
  created() {
    // ❌ warning, props are readonly!
    this.foo = 'bar'
  }
}
```

</div>

Aksar do cases hote hain jahan prop ko mutate karne ka mann karta hai:

1. **Prop ka use ek initial value ke roop mein hota hai; child component baad mein use apne local data property ke roop mein use karna chahta hai.** Is case mein, best yeh hai ki ek local data property define ki jaye jo prop ko apni initial value ke roop mein use kare:

   <div class="composition-api">

   ```js
   const props = defineProps(['initialCounter'])

   // counter only uses props.initialCounter as the initial value;
   // it is disconnected from future prop updates.
   const counter = ref(props.initialCounter)
   ```

   </div>
   <div class="options-api">

   ```js
   export default {
     props: ['initialCounter'],
     data() {
       return {
         // counter only uses this.initialCounter as the initial value;
         // it is disconnected from future prop updates.
         counter: this.initialCounter
       }
     }
   }
   ```

   </div>

2. **Prop ek raw value ke roop mein pass kiya gaya hai jise transform karne ki zarurat hai.** Is case mein, best yeh hai ki prop ke value ko use karke ek computed property define ki jaye:

   <div class="composition-api">

   ```js
   const props = defineProps(['size'])

   // computed property that auto-updates when the prop changes
   const normalizedSize = computed(() => props.size.trim().toLowerCase())
   ```

   </div>
   <div class="options-api">

   ```js
   export default {
     props: ['size'],
     computed: {
       // computed property that auto-updates when the prop changes
       normalizedSize() {
         return this.size.trim().toLowerCase()
       }
     }
   }
   ```

   </div>

### Mutating Object / Array Props {#mutating-object-array-props}

Jab objects aur arrays props ke roop mein pass kiye jaate hain, to child component prop binding ko mutate nahi kar sakta, lekin **wo object ya array ke nested properties ko mutate kar sakta hai.** Ye isliye hota hai kyunki JavaScript mein objects aur arrays reference ke through pass hote hain, aur Vue ke liye aisi mutations ko prevent karna kaafi mehenga process hota.

Is tarah ki mutation ka main nuksan ye hai ki child component parent state ko bina uski knowledge ke change kar sakta hai, jo future mein data flow ko samajhna mushkil bana deta hai. Best practice ke taur pe, aise mutations ko avoid karna chahiye jab tak parent aur child component ka design tightly coupled na ho. Aksar cases mein, child component ko [event emit karna chahiye](/guide/components/events) taaki parent mutation perform kare.

## Prop Validation {#prop-validation}

Components apne props ke liye kuch requirements specify kar sakte hain, jaise ki types jo aap pehle dekh chuke hain. Agar koi requirement match nahi hoti, to Vue browser ke JavaScript console mein warning deta hai. Ye especially tab useful hota hai jab aap ek aisa component develop kar rahe hote hain jo dusre developers use karne wale hain.

Prop validations specify karne ke liye, aap <span class="composition-api">`defineProps()` macro</span><span class="options-api">`props` option</span> ko ek object ke roop mein validation requirements ke saath de sakte hain, strings ke array ke bajaye. Jaise ki:

<div class="composition-api">

```js
defineProps({
  // Basic type check
  //  (`null` and `undefined` values will allow any type)
  propA: Number,
  // Multiple possible types
  propB: [String, Number],
  // Required string
  propC: {
    type: String,
    required: true
  },
  // Required but nullable string
  propD: {
    type: [String, null],
    required: true
  },
  // Number with a default value
  propE: {
    type: Number,
    default: 100
  },
  // Object with a default value
  propF: {
    type: Object,
    // Object or array defaults must be returned from
    // a factory function. The function receives the raw
    // props received by the component as the argument.
    default(rawProps) {
      return { message: 'hello' }
    }
  },
  // Custom validator function
  // full props passed as 2nd argument in 3.4+
  propG: {
    validator(value, props) {
      // The value must match one of these strings
      return ['success', 'warning', 'danger'].includes(value)
    }
  },
  // Function with a default value
  propH: {
    type: Function,
    // Unlike object or array default, this is not a factory
    // function - this is a function to serve as a default value
    default() {
      return 'Default function'
    }
  }
})
```

:::tip
`defineProps()` ke andar diya gaya code **kisi bhi dusre variable ko access nahi kar sakta jo `<script setup>` mein declare kiya gaya ho**, kyunki compilation ke time par ye pura expression ek outer function scope mein move ho jaata hai.
:::

</div>
<div class="options-api">

```js
export default {
  props: {
    // Basic type check
    //  (`null` and `undefined` values will allow any type)
    propA: Number,
    // Multiple possible types
    propB: [String, Number],
    // Required string
    propC: {
      type: String,
      required: true
    },
    // Required but nullable string
    propD: {
      type: [String, null],
      required: true
    },
    // Number with a default value
    propE: {
      type: Number,
      default: 100
    },
    // Object with a default value
    propF: {
      type: Object,
      // Object or array defaults must be returned from
      // a factory function. The function receives the raw
      // props received by the component as the argument.
      default(rawProps) {
        return { message: 'hello' }
      }
    },
    // Custom validator function
    // full props passed as 2nd argument in 3.4+
    propG: {
      validator(value, props) {
        // The value must match one of these strings
        return ['success', 'warning', 'danger'].includes(value)
      }
    },
    // Function with a default value
    propH: {
      type: Function,
      // Unlike object or array default, this is not a factory
      // function - this is a function to serve as a default value
      default() {
        return 'Default function'
      }
    }
  }
}
```

</div>

### Additional Details (Hinglish mein):

- By default, **saare props optional hote hain**, jab tak aap explicitly `required: true` specify na karo.

- Agar koi optional prop present nahi hai aur wo `Boolean` nahi hai, to uska value `undefined` hoga.

- Lekin agar prop `Boolean` type ka hai aur absent hai, to Vue usse automatically `false` maan lega. Aap is behavior ko change kar sakte ho by setting a `default` — jaise `default: undefined` likhne par wo ek non-Boolean prop ki tarah behave karega.

- Agar koi `default` value specify ki gayi hai, to wo tab use hoti hai jab final resolved prop value `undefined` ho — chahe prop absent ho ya usme explicitly `undefined` pass kiya gaya ho.

Agar prop validation fail ho jaati hai, to Vue console mein ek warning show karega (sirf development build mein).

<div class="composition-api">

Agar aap [Type-based props declarations](/api/sfc-script-setup#type-only-props-emit-declarations) <sup class="vt-badge ts" /> use kar rahe ho, to Vue try karta hai ki wo type annotations ko runtime prop declarations mein compile kar de. Jaise `defineProps<{ msg: string }>` compile ho jaata hai `{ msg: { type: String, required: true }}` mein.

</div>

<div class="options-api">

::: tip Note
Dhyan rahe ki props validate hote hain **component instance create hone se pehle**, to aap `default` ya `validator` functions ke andar `data`, `computed`, ya kisi bhi instance property ko access nahi kar sakte.
:::

</div>

### Runtime Type Checks {#runtime-type-checks}

`type` ka value in native constructors mein se koi bhi ho sakta hai:

- `String`
- `Number`
- `Boolean`
- `Array`
- `Object`
- `Date`
- `Function`
- `Symbol`
- `Error`

Iske alawa, `type` koi custom class ya constructor function bhi ho sakta hai — us case mein Vue `instanceof` check use karta hai. Jaise agar aap ke paas yeh class hai:

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName
    this.lastName = lastName
  }
}
```

You could use it as a prop's type:

<div class="composition-api">

```js
defineProps({
  author: Person
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    author: Person
  }
}
```

</div>

Vue `instanceof Person` ka use karega yeh validate karne ke liye ki `author` prop ka value sach mein `Person` class ka instance hai ya nahi.

### Nullable Type {#nullable-type}

Agar type required hai lekin nullable bhi ho sakta hai, to aap array syntax ka use kar sakte ho jisme `null` included ho:

<div class="composition-api">

```js
defineProps({
  id: {
    type: [String, null],
    required: true
  }
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    id: {
      type: [String, null],
      required: true
    }
  }
}
```

</div>

Dhyan rahe ki agar `type` sirf `null` diya gaya ho bina array syntax ke use kiye, to yeh kisi bhi type ko allow karega.

## Boolean Casting {#boolean-casting}

`Boolean` type waale props ke paas special casting rules hote hain jisse yeh native boolean attributes jaise behave kare. Maan lo ek `<MyComponent>` hai jiska declaration kuch aisa hai:

<div class="composition-api">

```js
defineProps({
  disabled: Boolean
})
```

</div>
<div class="options-api">

```js
export default {
  props: {
    disabled: Boolean
  }
}
```

</div>

Component ko kuch is tarah se use kiya ja sakta hai:

```vue-html
<!-- ye :disabled="true" pass karne ke barabar hai -->
<MyComponent disabled />

<!-- ye :disabled="false" pass karne ke barabar hai -->
<MyComponent />
```

Jab ek prop ko multiple types allow ki jati hain, tab bhi `Boolean` ke casting rules apply hote hain. Lekin ek special case hota hai jab `String` aur `Boolean` dono allow kiye jaate hain — Boolean casting rule sirf tab apply hoga jab `Boolean` pehle aata hai `String` se:

<div class="composition-api">

```js
// disabled will be casted to true
defineProps({
  disabled: [Boolean, Number]
})

// disabled will be casted to true
defineProps({
  disabled: [Boolean, String]
})

// disabled will be casted to true
defineProps({
  disabled: [Number, Boolean]
})

// disabled will be parsed as an empty string (disabled="")
defineProps({
  disabled: [String, Boolean]
})
```

</div>
<div class="options-api">

```js
// disabled will be casted to true
export default {
  props: {
    disabled: [Boolean, Number]
  }
}

// disabled will be casted to true
export default {
  props: {
    disabled: [Boolean, String]
  }
}

// disabled will be casted to true
export default {
  props: {
    disabled: [Number, Boolean]
  }
}

// disabled will be parsed as an empty string (disabled="")
export default {
  props: {
    disabled: [String, Boolean]
  }
}
```

</div>
