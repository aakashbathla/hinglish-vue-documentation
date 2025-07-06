# Components Basics {#components-basics}

<ScrimbaLink href="https://scrimba.com/links/vue-component-basics" title="Free Vue.js Components Basics Lesson" type="scrimba">
  Scrimba par ek interactive video lesson dekhein
</ScrimbaLink>

Components humein UI ko independent aur reusable pieces me divide karne ki suvidha dete hain, jisse hum har piece ko alag se soch kar design kar sakte hain. Ek app ko aksar ek nested component tree ke form me organize kiya jaata hai:

![Component Tree](./images/components.png)

<!-- https://www.figma.com/file/qa7WHDQRWuEZNRs7iZRZSI/components -->

Ye bilkul usi tarah hai jaise hum native HTML elements ko nest karte hain, lekin Vue apna khud ka component model implement karta hai jo humein har component ke andar custom content aur logic ko encapsulate karne ki suvidha deta hai. Vue native Web Components ke saath bhi achhi tarah se kaam karta hai. Agar aap Vue Components aur native Web Components ke beech ke relationship ke baare me curious hain, toh [yahan aur padhein](/guide/extras/web-components).

## Defining a Component {#defining-a-component}

Jab hum build step ka use karte hain, tab hum har Vue component ko ek dedicated file me `.vue` extension ke saath define karte hain — isse [Single-File Component](/guide/scaling-up/sfc) ya short me SFC kehte hain:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">You clicked me {{ count }} times.</button>
</template>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">You clicked me {{ count }} times.</button>
</template>
```

</div>

Jab aap build step ka use nahi kar rahe hote, tab ek Vue component ko ek simple JavaScript object ke roop me define kiya ja sakta hai jisme Vue-specific options hote hain:

<div class="options-api">

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      You clicked me {{ count }} times.
    </button>`
}
```

</div>
<div class="composition-api">

```js
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `
    <button @click="count++">
      You clicked me {{ count }} times.
    </button>`
  // Can also target an in-DOM template:
  // template: '#my-template-element'
}
```

</div>

Yahan template ko ek JavaScript string ke roop me inline kiya gaya hai, jise Vue runtime par compile karega. Aap ek ID selector ka bhi use kar sakte hain jo kisi element (aksar native `<template>` element) ko point karta ho — Vue us element ke content ko template source ke roop me use karega.

Upar wale example me ek single component define kiya gaya hai aur usse `.js` file ke default export ke roop me export kiya gaya hai, lekin aap named exports ka use karke ek hi file se multiple components bhi export kar sakte hain.

## Using a Component {#using-a-component}

:::tip
Hum is guide ke baaki hisson me SFC (Single File Component) syntax ka use karenge — components ke concepts wahi rahenge chahe aap build step ka use karein ya nahi. [Examples](/examples/) section dono scenarios me component usage ko dikhata hai.
:::

Ek child component ko use karne ke liye, humein usse parent component me import karna hota hai. Maal lijiye humne apna counter component `ButtonCounter.vue` file ke andar banaya hai, toh ye component us file ka default export hoga:

<div class="options-api">

```vue
<script>
import ButtonCounter from './ButtonCounter.vue'

export default {
  components: {
    ButtonCounter
  }
}
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

Imported component ko apne template me use karne ke liye, humein usse `components` option ke through [register](/guide/components/registration) karna hota hai. Uske baad component us key ke naam ke tag ke roop me template me available ho jaata hai jiske under usse register kiya gaya hai.

</div>

<div class="composition-api">

```vue
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

`<script setup>` ka use karte waqt, jo components aap import karte ho wo automatically template me available ho jaate hain.

</div>

Aap kisi component ko globally register bhi kar sakte hain, jisse wo poore app me kisi bhi component ke andar import kiye bina directly use ho sakta hai. Global vs. local registration ke pros aur cons ko detail me [Component Registration](/guide/components/registration) section me discuss kiya gaya hai.

Components ko jitni baar chahein, reuse kiya ja sakta hai:

```vue-html
<h1>Here are many child components!</h1>
<ButtonCounter />
<ButtonCounter />
<ButtonCounter />
```

<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNqVUE1LxDAQ/StjLqusNHotcfHj4l8QcontLBtsJiGdiFL6301SdrEqyEJyeG9m3ps3k3gIoXlPKFqhxi7awDtN1gUfGR4Ts6cnn4gxwj56B5tGrtgyutEEoAk/6lCPe5MGhqmwnc9KhMRjuxCwFi3UrCk/JU/uGTC6MBjGglgdbnfPGBFM/s7QJ3QHO/TfxC+UzD21d72zPItU8uQrrsWvnKsT/ZW2N2wur45BI3KKdETlFlmphZsF58j/RgdQr3UJuO8G273daVFFtlstahngxSeoNezBIUzTYgPzDGwdjk1VkYvMj4jzF0nwsyQ=)

</div>
<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNqVj91KAzEQhV/lmJsqlY3eSlr8ufEVhNys6ZQGNz8kE0GWfXez2SJUsdCLuZiZM9+ZM4qnGLvPQuJBqGySjYxMXOJWe+tiSIznwhz8SyieKWGfgsOqkyfTGbDSXsmFUG9rw+Ti0DPNHavD/faVEqGv5Xr/BXOwww4mVBNPnvOVklXTtKeO8qKhkj++4lb8+fL/mCMS7TEdAy6BtDfBZ65fVgA2s+L67uZMUEC9N0s8msGaj40W7Xa91qKtgbdQ0Ha0gyOM45E+TWDrKHeNIhfMr0DTN4U0me8=)

</div>

Dhyan dein ki jab aap buttons pe click karte ho, toh har button apna alag `count` maintain karta hai. Ye isliye hota hai kyunki jab bhi aap kisi component ko use karte ho, uska ek naya **instance** create hota hai.

SFCs me recommended hai ki aap child components ke liye `PascalCase` tag names ka use karein taaki unka distinction native HTML elements se clear rahe. Halanki native HTML tag names case-insensitive hote hain, lekin Vue SFC ek compiled format hai, jisme hum case-sensitive tag names ka use kar sakte hain. Hum tag ko close karne ke liye `/>` ka bhi use kar sakte hain.

Agar aap apne templates ko directly DOM me likh rahe hain (jaise ki ek native `<template>` element ke content ke roop me), toh wo browser ke native HTML parsing behavior ke under aayega. Aise cases me aapko `kebab-case` aur explicit closing tags ka use karna hoga components ke liye:

```vue-html
<!-- if this template is written in the DOM -->
<button-counter></button-counter>
<button-counter></button-counter>
<button-counter></button-counter>
```

Aur adhik jaankari ke liye dekhein [in-DOM template parsing caveats](#in-dom-template-parsing-caveats).

## Passing Props {#passing-props}

Agar hum ek blog bana rahe hain, toh humein aksar ek component ki zarurat padegi jo ek blog post ko represent kare. Hum chahenge ki sabhi blog posts ek jaise visual layout ka use karein, lekin har post ka content alag ho. Aisa component tabhi useful hoga jab aap usme data pass kar saken — jaise ki title aur specific post ka content. Yahi kaam props se hota hai.

Props custom attributes hote hain jo aap component par register kar sakte ho. Agar humein apne blog post component ko title pass karna hai, toh humein us title ko us component ke accepted props ke list me declare karna hoga, is tarah: <span class="options-api">[`props`](/api/options-state#props) option</span><span class="composition-api">[`defineProps`](/api/sfc-script-setup#defineprops-defineemits) macro</span> ka use karke:

<div class="options-api">

```vue
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title']
}
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

Jab kisi prop attribute ko koi value di jaati hai, toh wo value us component instance par ek property ban jaati hai. Ye property component ke template ke andar aur component ke `this` context me kisi bhi aur component property ki tarah accessible hoti hai.

</div>
<div class="composition-api">

```vue
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

`defineProps` ek compile-time macro hai jo sirf `<script setup>` ke andar available hota hai, aur ise explicitly import karne ki zarurat nahi hoti. Jo props aap declare karte ho wo automatically template me available ho jaate hain. `defineProps` ek object return karta hai jisme saare props hote hain jo component ko pass kiye gaye hain, taaki agar zarurat ho toh hum unhe JavaScript me access kar saken:

```js
const props = defineProps(['title'])
console.log(props.title)
```

Aur dekhein: [Typing Component Props](/guide/typescript/composition-api#typing-component-props) <sup class="vt-badge ts" />

Agar aap `<script setup>` ka use nahi kar rahe hain, toh props ko `props` option ke through declare kiya jaana chahiye, aur props object `setup()` function me first argument ke roop me pass hota hai:

```js
export default {
  props: ['title'],
  setup(props) {
    console.log(props.title)
  }
}
```

</div>

Ek component me aap jitne chahein utne props declare kar sakte hain, aur by default, kisi bhi prop ko koi bhi value di ja sakti hai.

Jaise hi ek prop register ho jaata hai, aap us prop ko ek custom attribute ke roop me data pass kar sakte hain, is tarah:

```vue-html
<BlogPost title="My journey with Vue" />
<BlogPost title="Blogging with Vue" />
<BlogPost title="Why Vue is so fun" />
```

Lekin ek typical app me, aapke parent component me aksar posts ka ek array hoga:

<div class="options-api">

```js
export default {
  // ...
  data() {
    return {
      posts: [
        { id: 1, title: 'My journey with Vue' },
        { id: 2, title: 'Blogging with Vue' },
        { id: 3, title: 'Why Vue is so fun' }
      ]
    }
  }
}
```

</div>
<div class="composition-api">

```js
const posts = ref([
  { id: 1, title: 'My journey with Vue' },
  { id: 2, title: 'Blogging with Vue' },
  { id: 3, title: 'Why Vue is so fun' }
])
```

</div>

Phir aap chahenge ki har post ke liye ek component render karein, `v-for` ka use karke:

```vue-html
<BlogPost
  v-for="post in posts"
  :key="post.id"
  :title="post.title"
 />
```

<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNp9UU1rhDAU/CtDLrawVfpxklRo74We2kPtQdaoaTUJ8bmtiP+9ia6uC2VBgjOZeXnz3sCejAkPnWAx4+3eSkNJqmRjtCU817p81S2hsLpBEEYL4Q1BqoBUid9Jmosi62rC4Nm9dn4lFLXxTGAt5dG482eeUXZ1vdxbQZ1VCwKM0zr3x4KBATKPcbsDSapFjOClx5d2JtHjR1KFN9fTsfbWcXdy+CZKqcqL+vuT/r3qvQqyRatRdMrpF/nn/DNhd7iPR+v8HCDRmDoj4RHxbfyUDjeFto8p8yEh1Rw2ZV4JxN+iP96FMvest8RTTws/gdmQ8HUr7ikere+yHduu62y//y3NWG38xIOpeODyXcoE8OohGYZ5VhhHHjl83sD4B3XgyGI=)

</div>
<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNp9kU9PhDAUxL/KpBfWBCH+OZEuid5N9qSHrQezFKhC27RlDSF8d1tYQBP1+N78OpN5HciD1sm54yQj1J6M0A6Wu07nTIpWK+MwwPASI0qjWkQejVbpsVHVQVl30ZJ0WQRHjwFMnpT0gPZLi32w2h2DMEAUGW5iOOEaniF66vGuOiN5j0/hajx7B4zxxt5ubIiphKz+IO828qXugw5hYRXKTnqSydcrJmk61/VF/eB4q5s3x8Pk6FJjauDO16Uye0ZCBwg5d2EkkED2wfuLlogibMOTbMpf9tMwP8jpeiMfRdM1l8Tk+/F++Y6Cl0Lyg1Ha7o7R5Bn9WwSg9X0+DPMxMI409fPP1PELlVmwdQ==)

</div>

Note karein ki yahan [`v-bind` syntax](/api/built-in-directives#v-bind) (`:title="post.title"`) ka use kiya gaya hai taaki dynamic prop values pass ki ja sakein. Ye tab especially useful hota hai jab aapko pehle se yeh nahi pata hota ki kya content render karna hai.

Abhi ke liye, props ke baare me itna hi kaafi hai. Lekin jab aap is page ka content achhi tarah samajh lein, tab hum recommend karte hain ki aap baad me [Props](/guide/components/props) par detailed guide bhi padhein.

## Listening to Events {#listening-to-events}

Jab hum apne `<BlogPost>` component ko develop karte hain, toh kuch features aise ho sakte hain jinke liye parent component se communicate karna zaruri ho. Jaise ki, maan lijiye hum ek accessibility feature add karna chahte hain jisme blog post ka text size badhaya ja sake, lekin page ke baaki parts ke size me koi badlav na ho.

Parent component me, hum is feature ko support kar sakte hain ek `postFontSize` <span class="options-api">data property</span><span class="composition-api">ref</span> ke zariye:

<div class="options-api">

```js{6}
data() {
  return {
    posts: [
      /* ... */
    ],
    postFontSize: 1
  }
}
```

</div>
<div class="composition-api">

```js{5}
const posts = ref([
  /* ... */
])

const postFontSize = ref(1)
```

</div>

Is property ka use hum template me sabhi blog posts ka font size control karne ke liye kar sakte hain:

```vue-html{1,7}
<div :style="{ fontSize: postFontSize + 'em' }">
  <BlogPost
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
   />
</div>
```

Ab chaliye ek button add karte hain `<BlogPost>` component ke template me:

```vue{5}
<!-- BlogPost.vue, omitting <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button>Enlarge text</button>
  </div>
</template>
```

Abhi tak button kuch karta nahi hai – hum chahte hain ki button par click karne se parent component ko ye signal mile ki sabhi posts ka text bada kar diya jaye. Is problem ko solve karne ke liye, components ek custom events system provide karte hain. Parent component child component instance ke kisi bhi event ko `v-on` ya `@` ke zariye sun sakta hai, bilkul waise hi jaise hum native DOM events ke saath karte hain:

```vue-html{3}
<BlogPost
  ...
  @enlarge-text="postFontSize += 0.1"
 />
```

Phir child component apne upar ek event emit kar sakta hai built-in [**`$emit`** method](/api/component-instance#emit) ko call karke, jisme event ka naam pass kiya jaata hai:

```vue{5}
<!-- BlogPost.vue, omitting <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
```

`@enlarge-text="postFontSize += 0.1"` listener ke wajah se, parent component ko event receive hoga aur wo `postFontSize` ki value ko update karega.

<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNqNUsFOg0AQ/ZUJMaGNbbHqidCmmujNxMRED9IDhYWuhV0CQy0S/t1ZYIEmaiRkw8y8N/vmMZVxl6aLY8EM23ByP+Mprl3Bk1RmCPexjJ5ljhBmMgFzYemEIpiuAHAFOzXQgIVeESNUKutL4gsmMLfbBPStVFTP1Bl46E2mup4xLDKhI4CUsMR+1zFABTywYTkD5BgzG8ynEj4kkVgJnxz38Eqaut5jxvXAUCIiLqI/8TcD/m1fKhTwHHIJYSEIr+HbnqikPkqBL/yLSMs23eDooNexel8pQJaksYeMIgAn4EewcyxjtnKNCsK+zbgpXILJEnW30bCIN7ZTPcd5KDNqoWjARWufa+iyfWBlV13wYJRvJtWVJhiKGyZiL4vYHNkJO8wgaQVXi6UGr51+Ndq5LBqMvhyrH9eYGePtOVu3n3YozWSqFsBsVJmt3SzhzVaYY2nm9l82+7GX5zTGjlTM1SyNmy5SeX+7rqr2r0NdOxbFXWVXIEoBGz/m/oHIF0rB5Pz6KTV6aBOgEo7Vsn51ov4GgAAf2A==)

</div>
<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNp1Uk1PwkAQ/SuTxqQYgYp6ahaiJngzITHRA/UAZQor7W7TnaK16X93th8UEuHEvPdm5s3bls5Tmo4POTq+I0yYyZTAIOXpLFAySXVGUEKGEVQQZToBl6XukXqO9XahDbXc2OsAO5FlAIEKtWJByqCBqR01WFqiBLnxYTIEkhSjD+5rAV86zxQW8C1pB+88Aaphr73rtXbNVqrtBeV9r/zYFZYHacBoiHLFykB9Xgfq1NmLVvQmf7E1OGFaeE0anAMXhEkarwhtRWIjD+AbKmKcBk4JUdvtn8+6ARcTu87hLuCf6NJpSoDDKNIZj7BtIFUTUuB0tL/HomXHcnOC18d1TF305COqeJVtcUT4Q62mtzSF2/GkE8/E8b1qh8Ljw/if8I7nOkPn9En/+Ug2GEmFi0ynZrB0azOujbfB54kki5+aqumL8bING28Yr4xh+2vePrI39CnuHmZl2TwwVJXwuG6ZdU6kFTyGsQz33HyFvH5wvvyaB80bACwgvKbrYgLVH979DQc=)

</div>

Hum optionally emitted events ko declare bhi kar sakte hain <span class="options-api">[`emits`](/api/options-state#emits) option</span><span class="composition-api">[`defineEmits`](/api/sfc-script-setup#defineprops-defineemits) macro</span> ka use karke:

<div class="options-api">

```vue{5}
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title'],
  emits: ['enlarge-text']
}
</script>
```

</div>
<div class="composition-api">

```vue{4}
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
defineEmits(['enlarge-text'])
</script>
```

</div>

Ye component ke dwara emit kiye gaye saare events ko document karta hai aur optionally unhe [validate](/guide/components/events#events-validation) bhi karta hai. Isse Vue ko ye samajhne mein madad milti hai ki in events ko child component ke root element par native listeners ke roop mein implicitly apply nahi karna chahiye.

<div class="composition-api">

`defineProps` ki tarah hi, `defineEmits` bhi sirf `<script setup>` ke andar hi use kiya ja sakta hai aur ise import karne ki zarurat nahi hoti. Ye ek `emit` function return karta hai jo `$emit` method ke barabar hota hai. Isse hum component ke `<script setup>` section mein events emit karne ke liye use kar sakte hain, jahan `$emit` directly accessible nahi hota:

```vue
<script setup>
const emit = defineEmits(['enlarge-text'])

emit('enlarge-text')
</script>
```

See also: [Typing Component Emits](/guide/typescript/composition-api#typing-component-emits) <sup class="vt-badge ts" />

Agar aap `<script setup>` ka use nahi kar rahe hain, toh aap `emits` option ka use karke emit hone wale events ko declare kar sakte hain. Aap `emit` function ko setup context ke property ke roop mein access kar sakte hain (jo `setup()` function ko second argument ke roop mein pass kiya jata hai):

```js
export default {
  emits: ['enlarge-text'],
  setup(props, ctx) {
    ctx.emit('enlarge-text')
  }
}
```

</div>

Yeh sab kuch tha jo aapko abhi ke liye custom component events ke baare mein jaanna zaroori hai. Jab aap is page ko poori tarah padh chuke ho aur uska content samajh chuke ho, tab hum recommend karte hain ki aap [Custom Events](/guide/components/events) ke full guide par wapas laut kar usse detail mein padhein.

## Content Distribution with Slots {#content-distribution-with-slots}

Bilkul jaise hum HTML elements ke andar content pass karte hain, waise hi Vue components mein bhi aksar yeh kaafi useful hota hai ki hum unke andar kuch content pass kar saken. Jaise ki:

```vue-html
<AlertBox>
  Something bad happened.
</AlertBox>
```

Jo upar diya gaya output hai:

:::danger This is an Error for Demo Purposes
Something bad happened.
:::

Ye Vue ke custom `<slot>` element ka use karke achieve kiya ja sakta hai:

```vue{5}
<!-- AlertBox.vue -->
<template>
  <div class="alert-box">
    <strong>This is an Error for Demo Purposes</strong>
    <slot />
  </div>
</template>

<style scoped>
.alert-box {
  /* ... */
}
</style>
```

Jaise ki aap upar dekh pa rahe ho, hum `<slot>` ko ek placeholder ke roop mein use karte hain jahan par humein content inject karna hota hai – aur bas! Ho gaya kaam!

<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNpVUcFOwzAM/RUTDruwFhCaUCmThsQXcO0lbbKtIo0jx52Kpv07TreWouTynl+en52z2oWQnXqrClXGhtrA28q3XUBi2DlL/IED7Ak7WGX5RKQHq8oDVN4Oo9TYve4dwzmxDcp7bz3HAs5/LpfKyy3zuY0Atl1wmm1CXE5SQeLNX9hZPrb+ALU2cNQhWG9NNkrnLKIt89lGPahlyDTVogVAadoTNE7H+F4pnZTrGodKjUUpRyb0h+0nEdKdRL3CW7GmfNY5ZLiiMhfP/ynG0SL/OAuxwWCNMNncbVqSQyrgfrPZvCVcIxkrxFMYIKJrDZA1i8qatGl72ehLGEY6aGNkNwU8P96YWjffB8Lem/Xkvn9NR6qy+fRd14FSgopvmtQmzTT9Toq9VZdfIpa5jQ==)

</div>
<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNpVUEtOwzAQvcpgFt3QBBCqUAiRisQJ2GbjxG4a4Xis8aQKqnp37PyUyqv3mZn3fBVH55JLr0Umcl9T6xi85t4VpW07h8RwNJr4Cwc4EXawS9KFiGO70ubpNBcmAmDdOSNZR8T5Yg0IoOQf7DSfW9tAJRWcpXPaapWM1nVt8ObpukY8ie29GHNzAiBX7QVqI73/LIWMzn2FQylGMcieCW1TfBMhPYSoE5zFitLVZ5BhQnkadt6nGKt5/jMafI1Oq8Ak6zW4xrEaDVIGj4fD4SPiCknpQLy4ATyaVgFptVH2JFXb+wze3DDSTioV/iaD1+eZqWT92xD2Vu2X7af3+IJ6G7/UToVigpJnTzwTO42eWDnELsTtH/wUqH4=)

</div>

Abhi ke liye slots ke baare mein aapko sirf itna hi jaanne ki zarurat hai, lekin jab aap is page ka content achhi tarah samajh jaayein, tab hum recommend karte hain ki aap [Slots](/guide/components/slots) ke full guide ko wapas jaake padhein.

## Dynamic Components {#dynamic-components}

Kabhi-kabhi aisa hota hai ki humein dynamically components ke beech switch karna padta hai, jaise ki ek tabbed interface mein:

<div class="options-api">

[Open example in the Playground](https://play.vuejs.org/#eNqNVE2PmzAQ/Ssj9kArLSHbrXpwk1X31mMPvS17cIxJrICNbJMmivLfO/7AEG2jRiDkefP85sNmztlr3y8OA89ItjJMi96+VFJ0vdIWfqqOQ6NVB/midIYj5sn9Sxlrkt9b14RXzXbiMElEO5IAKsmPnljzhg6thbNDmcLdkktrSADAJ/IYlj5MXEc9Z1w8VFNLP30ed2luBy1HC4UHrVH2N90QyJ1kHnUALN1gtLeIQu6juEUMkb8H5sXHqiS+qzK1Cw3Lu76llqMFsKrFAVhLjVlXWc07VWUeR89msFbhhhAWDkWjNJIwPgjp06iy5CV7fgrOOTgKv+XoKIIgpnoGyiymSmZ1wnq9dqJweZ8p/GCtYHtUmBMdLXFitgDnc9ju68b0yxDO1WzRTEcFRLiUJsEqSw3wwi+rMpFDj0psEq5W5ax1aBp7at1y4foWzq5R0hYN7UR7ImCoNIXhWjTfnW+jdM01gaf+CEa1ooYHzvnMVWhaiwEP90t/9HBP61rILQJL3POMHw93VG+FLKzqUYx3c2yjsOaOwNeRO2B8zKHlzBKQWJNH1YHrplV/iiMBOliFILYNK5mOKdSTMviGCTyNojFdTKBoeWNT3s8f/Vpsd7cIV61gjHkXnotR6OqVkJbrQKdsv9VqkDWBh2bpnn8VXaDcHPexE4wFzsojO9eDUOSVPF+65wN/EW7sHRsi5XaFqaexn+EH9Xcpe8zG2eWG3O0/NVzUaeJMk+jGhUXlNPXulw5j8w7t2bi8X32cuf/Vv/wF/SL98A==)

</div>
<div class="composition-api">

[Open example in the Playground](https://play.vuejs.org/#eNqNVMGOmzAQ/ZURe2BXCiHbrXpwk1X31mMPvS1V5RiTWAEb2SZNhPLvHdvggLZRE6TIM/P8/N5gpk/e2nZ57HhCkrVhWrQWDLdd+1pI0bRKW/iuGg6VVg2ky9wFDp7G8g9lrIl1H80Bb5rtxfFKMcRzUA+aV3AZQKEEhWRKGgus05pL+5NuYeNwj6mTkT4VckRYujVY63GT17twC6/Fr4YjC3kp5DoPNtEgBpY3bU0txwhgXYojsJoasymSkjeqSHweK9vOWoUbXIC/Y1YpjaDH3wt39hMI6TUUSYSQAz8jArPT5Mj+nmIhC6zpAu1TZlEhmXndbBwpXH5NGL6xWrADMsyaMj1lkAzQ92E7mvYe8nCcM24xZApbL5ECiHCSnP73KyseGnvh6V/XedwS2pVjv3C1ziddxNDYc+2WS9fC8E4qJW1W0UbUZwKGSpMZrkX11dW2SpdcE3huT2BULUp44JxPSpmmpegMgU/tyadbWpZC7jCxwj0v+OfTDdU7ITOrWiTjzTS3Vei8IfB5xHZ4PmqoObMEJHryWXXkuqrVn+xEgHZWYRKbh06uLyv4iQq+oIDnkXSQiwKymlc26n75WNdit78FmLWCMeZL+GKMwlKrhLRcBzhlh51WnSwJPFQr9/zLdIZ007w/O6bR4MQe2bseBJMzer5yzwf8MtzbOzYMkNsOY0+HfoZv1d+lZJGMg8fNqdsfbbio4b77uRVv7I0Li8xxZN1PHWbeHdyTWXc/+zgw/8t/+QsROe9h)

</div>

Upar diya gaya example Vue ke `<component>` element aur uske special `is` attribute ke zariye possible hota hai:

<div class="options-api">

```vue-html
<!-- Component changes when currentTab changes -->
<component :is="currentTab"></component>
```

</div>
<div class="composition-api">

```vue-html
<!-- Component changes when currentTab changes -->
<component :is="tabs[currentTab]"></component>
```

</div>

Upar wale example mein, jo value `:is` ko pass ki jaati hai wo ya toh:

- ek registered component ka name string ho sakta hai, YA
- directly imported component object ho sakta hai

Aap `is` attribute ka use regular HTML elements banane ke liye bhi kar sakte ho.

Jab aap `<component :is="...">` ka use karke multiple components ke beech switch karte ho, toh jis component se switch kiya ja raha hai, wo unmount ho jaata hai. Agar aap chahte ho ki inactive components memory me bane rahen, toh aap built-in [`<KeepAlive>` component](/guide/built-ins/keep-alive) ka use kar sakte ho.

## in-DOM Template Parsing Caveats {#in-dom-template-parsing-caveats}

Agar aap apne Vue templates ko directly DOM ke andar likh rahe hain, toh Vue ko template string ko DOM se retrieve karna padega. Is wajah se kuch limitations hoti hain jo browsers ke native HTML parsing behavior ke kaaran hoti hain.

:::tip
Yeh dhyan dena zaroori hai ki neeche discuss ki gayi limitations sirf tab apply hoti hain jab aap templates directly DOM me likh rahe ho. Ye limitations apply nahi hoti agar aap in sources ka use kar rahe ho:

- Single-File Components
- Inlined template strings (jaise `template: '...'`)
- `<script type="text/x-template">`
  :::

### Case Insensitivity {#case-insensitivity}

HTML tags aur attribute names case-insensitive hote hain, isliye browsers kisi bhi uppercase character ko lowercase me interpret karte hain. Iska matlab hai ki jab aap in-DOM templates ka use karte ho, toh PascalCase component names aur camelCased prop names ya `v-on` event names ko kebab-case (hyphen-delimited) format me likhna padega:

```js
// camelCase in JavaScript
const BlogPost = {
  props: ['postTitle'],
  emits: ['updatePost'],
  template: `
    <h3>{{ postTitle }}</h3>
  `
}
```

```vue-html
<!-- kebab-case in HTML -->
<blog-post post-title="hello!" @update-post="onUpdatePost"></blog-post>
```

### Self Closing Tags {#self-closing-tags}

Pichle code samples mein humne components ke liye self-closing tags ka use kiya hai:

```vue-html
<MyComponent />
```

Yeh isliye hai kyunki Vue ka template parser `/>` ko kisi bhi tag ke end hone ka indication maanta hai, chahe wo kisi bhi type ka ho.

Lekin in-DOM templates mein, humein hamesha explicit closing tags ka use karna padta hai:

```vue-html
<my-component></my-component>
```

Yeh isliye hota hai kyunki HTML specification sirf [kuch specific elements](https://html.spec.whatwg.org/multipage/syntax.html#void-elements) ko hi closing tags ke bina allow karta hai — sabse common hain `<input>` aur `<img>`.

Baaki sab elements ke liye, agar aap closing tag omit kar dete ho, toh native HTML parser sochega ki aapne opening tag kabhi close hi nahi kiya. Jaise, yeh snippet:

```vue-html
<my-component /> <!-- we intend to close the tag here... -->
<span>hello</span>
```

yeh parse hoga kuch is tarah:

```vue-html
<my-component>
  <span>hello</span>
</my-component> <!-- lekin browser yahan par close kar dega. -->
```

### Element Placement Restrictions {#element-placement-restrictions}

Kuch HTML elements jaise `<ul>`, `<ol>`, `<table>` aur `<select>` ke andar kaunse elements aa sakte hain, ispar kuch restrictions hoti hain. Aur kuch elements jaise `<li>`, `<tr>`, aur `<option>` sirf kuch specific elements ke andar hi valid hote hain.

Aise cases mein, agar aap in restrictions wale elements ke saath Vue components ka use karte ho, toh problems ho sakti hain. Jaise ki:

```vue-html
<table>
  <blog-post-row></blog-post-row>
</table>
```

Custom component `<blog-post-row>` ko browser invalid content samajh ke hoist kar dega, jiske wajah se rendered output mein errors aayenge. Iska solution hai special [`is` attribute](/api/built-in-special-attributes#is) ka use karna:

```vue-html
<table>
  <tr is="vue:blog-post-row"></tr>
</table>
```

:::tip
Jab `is` attribute ko native HTML elements par use kiya jaata hai, toh uski value ko `vue:` prefix ke saath likhna zaroori hota hai taaki Vue usse ek Vue component ke roop mein samjhe. Ye zaroori hai taaki browser ke native [customized built-in elements](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example) ke saath confusion na ho.
:::

Itna hi kaafi hai in-DOM template parsing caveats ke baare mein — aur waise bhi, ye Vue ke _Essentials_ ka end hai. Mubarak ho! Abhi aur bhi cheezein seekhni baaki hain, lekin sabse pehle ek chhota break lo aur Vue ke saath khud experiment karo — kuchh mazedaar banao, ya fir [Examples](/examples/) section explore karo agar abhi tak nahi kiya hai.

Jab tumhe ab tak ka knowledge comfortable lage, tab guide mein aage badho aur components ke baare mein aur gehraai se seekho.
