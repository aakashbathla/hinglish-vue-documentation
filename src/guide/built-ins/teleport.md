# Teleport {#teleport}

 <VueSchoolLink href="https://vueschool.io/lessons/vue-3-teleport" title="Free Vue.js Teleport Lesson"/>

`<Teleport>` ek built-in component hai jo humein allow karta hai ki hum apne component ke template ka ek part kisi aise DOM node me "teleport" kar dein jo us component ki DOM hierarchy ke bahar exist karta ho.

## Basic Usage {#basic-usage}

Kabhi-kabhi ek component ke template ka kuch part logically usi ka hota hai, lekin visual perspective se usse DOM me kahin aur dikhana better hota hai, chahe wo Vue application ke bahar hi kyu na ho.

Iska sabse common example hota hai jab hum ek full-screen modal banate hain. Ideally, modal ka button aur modal ka code ek hi single-file component me likhna chahiye, kyunki dono ka relation open / close state se hai. Lekin iska matlab ye bhi hai ki modal button ke sath render hoga, application ke DOM hierarchy me deeply nested hokar. Ye CSS se modal ko position karte waqt tricky issues create kar sakta hai.

Socho niche wale HTML structure ko:

```vue-html
<div class="outer">
  <h3>Vue Teleport Example</h3>
  <div>
    <MyModal />
  </div>
</div>
```

Aur yeh hai `<MyModal>` ka implementation:

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

const open = ref(false)
</script>

<template>
  <button @click="open = true">Open Modal</button>

  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
}
</style>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      open: false
    }
  }
}
</script>

<template>
  <button @click="open = true">Open Modal</button>

  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
}
</style>
```

</div>

Component ke andar ek `<button>` hota hai jo modal ko open karne ke liye trigger karta hai, aur ek `<div>` hota hai jisme `.modal` class lagi hoti hai. Ye modal ka content rakhta hai aur ek button hota hai jisse modal khud close ho jata hai.

Jab hum is component ko initial HTML structure me use karte hain, to kuch potential issues aa sakte hain:

- `position: fixed` tabhi element ko viewport ke relative place karta hai jab koi ancestor element me `transform`, `perspective` ya `filter` property set na ho. Agar hum ancestor `<div class="outer">` ko CSS transform ke saath animate karna chahe, to modal ka layout toot jayega!

- Modal ka `z-index` uske containing elements ke andar hi constrained rahega. Agar koi aur element ho jo `<div class="outer">` ke upar overlap kare aur uska `z-index` zyada ho, to wo hamare modal ko cover kar dega.

`<Teleport>` ek clean solution deta hai in problems ko solve karne ke liye, kyunki ye hume nested DOM structure se bahar nikalne ki flexibility deta hai. Ab chalo `<MyModal>` ko modify karte hain `<Teleport>` use karne ke liye:

```vue-html{3,8}
<button @click="open = true">Open Modal</button>

<Teleport to="body">
  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</Teleport>
```

`<Teleport>` ka `to` target ek CSS selector string ya phir ek actual DOM node expect karta hai. Yaha hum basically Vue ko bol rahe hain: "**is template fragment ko teleport karo body tag ke andar**".

Aap niche diye gaye button ko click karke apne browser ke devtools me `<body>` tag inspect kar sakte ho:

<script setup>
import { ref } from 'vue'
const open = ref(false)
</script>

<div class="demo">
  <button @click="open = true">Open Modal</button>
  <ClientOnly>
    <Teleport to="body">
      <div v-if="open" class="demo modal-demo">
        <p style="margin-bottom:20px">Hello from the modal!</p>
        <button @click="open = false">Close</button>
      </div>
    </Teleport>
  </ClientOnly>
</div>

<style>
.modal-demo {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
  background-color: var(--vt-c-bg);
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}
</style>

Aap `<Teleport>` ko [`<Transition>`](./transition) ke saath combine karke animated modals bana sakte ho - [example yaha dekho](/examples/#modal).

:::tip
Teleport ka `to` target `<Teleport>` component mount hone se pehle hi DOM me present hona chahiye. Ideally, ye poore Vue application ke bahar ek element hona chahiye. Agar aap Vue ke kisi aur element ko target kar rahe ho, to ensure karo ki wo element `<Teleport>` se pehle mount ho jaye.  
:::

## Using with Components {#using-with-components}

`<Teleport>` sirf rendered DOM structure ko alter karta hai - ye components ki logical hierarchy ko affect nahi karta. Matlab agar `<Teleport>` ke andar koi component hai, to wo hamesha us parent component ka logical child hi rahega jisme `<Teleport>` likha gaya hai. Props passing aur event emitting same tarike se kaam karte rahenge.

Iska matlab ye bhi hai ki parent component se aane wali injections expected tarike se work karengi, aur child component Vue Devtools me parent ke niche hi dikhai dega — na ki us jagah jaha content actually move hua hai.

## Disabling Teleport {#disabling-teleport}

Kabhi-kabhi hume `<Teleport>` ko conditionally disable karna padta hai. Jaise desktop par ek component overlay ke roop me render karna hai, lekin mobile par inline dikhana hai. `<Teleport>` is case me `disabled` prop support karta hai jise dynamically toggle kiya ja sakta hai.

```vue-html
<Teleport :disabled="isMobile">
  ...
</Teleport>
```

Phir hum dynamically `isMobile` ko update kar sakte hain.

## Multiple Teleports on the Same Target {#multiple-teleports-on-the-same-target}

Ek common use case hota hai ek reusable `<Modal>` component ka, jisme ek hi time par multiple instances active ho sakte hain. Aise scenario me multiple `<Teleport>` components apna content same target element me mount kar sakte hain. Order simple append hoga — jo baad me mount hoga wo pehle wale ke baad place hoga — lekin sabhi target element ke andar hi rahenge.

Niche diye gaye usage ko dekho:

```vue-html
<Teleport to="#modals">
  <div>A</div>
</Teleport>
<Teleport to="#modals">
  <div>B</div>
</Teleport>
```

Rendered result kuch is tarah hoga:

```html
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```

## Deferred Teleport <sup class="vt-badge" data-text="3.5+" /> {#deferred-teleport}

Vue 3.5 aur uske upar, hum `defer` prop ka use karke Teleport ka target resolving defer kar sakte hain jab tak application ke dusre parts mount nahi ho jate. Isse hume allow milta hai ki Teleport ek aise container element ko target kare jo Vue ke through render hota hai, lekin component tree ke later part me.

```vue-html
<Teleport defer to="#late-div">...</Teleport>

<!-- somewhere later in the template -->
<div id="late-div"></div>
```

Note karo ki target element ko Teleport ke sath hi same mount / update tick me render hona chahiye — matlab agar `<div>` ek second baad mount hota hai, to Teleport fir bhi error throw karega. `defer` kaam karta hai bilkul `mounted` lifecycle hook jaisa.

---

**Related**

- [`<Teleport>` API reference](/api/built-in-components#teleport)
- [Handling Teleports in SSR](/guide/scaling-up/ssr#teleports)
