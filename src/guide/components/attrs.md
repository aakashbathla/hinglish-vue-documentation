---
outline: deep
---

# Fallthrough Attributes {#fallthrough-attributes}

> Ye page maan ke chalta hai ki aap pehle se [Components Basics](/guide/essentials/component-basics) padh chuke ho. Agar aap components me naye ho to pehle usse padhein.

## Attribute Inheritance {#attribute-inheritance}

Ek "fallthrough attribute" wo attribute ya `v-on` event listener hota hai jo component ko pass kiya jata hai, lekin receiving component ke [props](./props) ya [emits](./events#declaring-emitted-events) me explicitly declare nahi kiya gaya hota. Iske common examples hain `class`, `style`, aur `id` attributes.

Jab koi component ek single root element render karta hai, to fallthrough attributes automatically us root element ke attributes me add ho jate hain. Example ke liye, agar ek `<MyButton>` component ke paas ye template ho:

```vue-html
<!-- template of <MyButton> -->
<button>Click Me</button>
```

Aur ek parent is component ko aise use kare:

```vue-html
<MyButton class="large" />
```

Final rendered DOM kuch is tarah dikhega:

```html
<button class="large">Click Me</button>
```

Yahan, `<MyButton>` ne `class` ko ek accepted prop ke roop mein declare nahi kiya. Isliye, `class` ko ek fallthrough attribute ke roop mein treat kiya gaya aur automatically `<MyButton>` ke root element mein add kar diya gaya.

### `class` aur `style` Merging {#class-and-style-merging}

Agar child component ke root element me already `class` ya `style` attributes maujood hain, to wo parent se inherit hone wale `class` aur `style` values ke saath merge ho jate hain. Maan lijiye hum pehle wale example me `<MyButton>` ka template aise change karte hain:

```vue-html
<!-- template of <MyButton> -->
<button class="btn">Click Me</button>
```

Then the final rendered DOM would now become:

```html
<button class="btn large">Click Me</button>
```

### `v-on` Listener Inheritance {#v-on-listener-inheritance}

The same rule applies to `v-on` event listeners:

```vue-html
<MyButton @click="onClick" />
```

`click` listener `<MyButton>` ke root element pe add ho jayega, yaani ki native `<button>` element pe. Jab native `<button>` click hoga, to parent component ka `onClick` method trigger hoga. Agar native `<button>` pe pehle se hi `v-on` ke through koi `click` listener bind hai, to dono listeners trigger honge.

### Nested Component Inheritance {#nested-component-inheritance}

Agar ek component kisi doosre component ko apna root node banata hai — jaise agar hum `<MyButton>` ko refactor karke `<BaseButton>` ko uska root banate hain:

```vue-html
<!-- template of <MyButton/> that simply renders another component -->
<BaseButton />
```

Jab `<MyButton>` fallthrough attributes receive karta hai, to ye attributes automatically `<BaseButton>` ko forward ho jate hain.

Dhyan rahe:

1. Forwarded attributes mein koi bhi prop ya `v-on` listener include nahi hota jo `<MyButton>` ke through declare kiya gaya ho — matlab, jo props ya listeners declare kiye gaye hain, unko `<MyButton>` "consume" kar leta hai.

2. Forwarded attributes ko `<BaseButton>` apne declared props ke roop mein accept kar sakta hai, agar usne woh declare kiye ho.

## Attribute Inheritance ko Disable Karna {#disabling-attribute-inheritance}

Agar aap nahi chahte ki koi component automatically attributes inherit kare, to aap component options mein `inheritAttrs: false` set kar sakte ho.

<div class="composition-api">

Vue 3.3 se, aap `<script setup>` ke andar directly [`defineOptions`](/api/sfc-script-setup#defineoptions) use kar sakte ho:

```vue
<script setup>
defineOptions({
  inheritAttrs: false
})
// ...setup logic
</script>
```

</div>

Common scenario jahan `inheritAttrs` disable karte hain, wo hota hai jab attributes ko root node ke alawa kisi aur element pe apply karna hota hai. Jab `inheritAttrs: false` set kiya jata hai, tab aapko poori control milti hai ki fallthrough attributes ko kis element pe lagana hai.

Ye fallthrough attributes directly template expressions mein `$attrs` ke through access kiye ja sakte hain:

```vue-html
<span>Fallthrough attributes: {{ $attrs }}</span>
```

`$attrs` object mein saare attributes hote hain jo component ke `props` ya `emits` options mein declare nahi kiye gaye (jaise `class`, `style`, `v-on` listeners, etc.).

Kuch baatein dhyan dene layak:

- Props ke comparison mein, fallthrough attributes JavaScript mein apna original casing preserve karte hain, to `foo-bar` ko access karne ke liye aapko `$attrs['foo-bar']` likhna padega.

- `v-on` event listener jaise `@click` ko `$attrs.onClick` ke roop mein function ke form mein access kiya jata hai.

Agar hum `<MyButton>` component ka example lein [previous section](#attribute-inheritance) se — kabhi-kabhi hume `<button>` ko ek extra `<div>` ke andar wrap karna padta hai styling ke liye:

```vue-html
<div class="btn-wrapper">
  <button class="btn">Click Me</button>
</div>
```

Hum chahte hain ki saare fallthrough attributes jaise `class` aur `v-on` listeners inner `<button>` pe lage, outer `<div>` pe nahi. Isko achieve karne ke liye hum `inheritAttrs: false` aur `v-bind="$attrs"` ka use kar sakte hain:

```vue-html{2}
<div class="btn-wrapper">
  <button class="btn" v-bind="$attrs">Click Me</button>
</div>
```

Yad rahe ki [`v-bind` bina kisi argument ke](/guide/essentials/template-syntax#dynamically-binding-multiple-attributes) object ke saare properties ko target element ke attributes ke roop mein bind karta hai.

## Attribute Inheritance on Multiple Root Nodes {#attribute-inheritance-on-multiple-root-nodes}

Single root node wale components ke comparison mein, multiple root nodes wale components mein automatic attribute fallthrough nahi hota. Agar `$attrs` ko explicitly bind nahi kiya gaya ho, to runtime warning aayegi.

```vue-html
<CustomLayout id="custom-layout" @click="changeValue" />
```

Agar `<CustomLayout>` ka template kuch aisa hai jisme multiple roots hain:

```vue-html
<header>...</header>
<main>...</main>
<footer>...</footer>
```

To warning aayegi, kyunki Vue ko nahi pata ki fallthrough attributes ko kahan apply karna hai.

Agar aap `$attrs` ko explicitly bind kar do, to warning nahi aayegi:

```vue-html{2}
<header>...</header>
<main v-bind="$attrs">...</main>
<footer>...</footer>
```

## JavaScript mein Fallthrough Attributes ko Access Karna {#accessing-fallthrough-attributes-in-javascript}

<div class="composition-api">

Agar zarurat ho, to aap `<script setup>` ke andar `useAttrs()` API ka use karke component ke fallthrough attributes access kar sakte ho:

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()
</script>
```

Agar aap `<script setup>` use nahi kar rahe, to `attrs` `setup()` context ke property ke roop mein milta hai:

```js
export default {
  setup(props, ctx) {
    // fallthrough attributes ko ctx.attrs ke through access karein
    console.log(ctx.attrs)
  }
}
```

Dhyan rahe ki `attrs` object hamesha latest fallthrough attributes ko reflect karta hai, lekin ye reactive nahi hota (performance reasons ke wajah se). Aap iske changes ko watch nahi kar sakte. Agar reactivity chahiye, to prop ka use karein. Ya fir aap `onUpdated()` ka use kar sakte ho taaki har update pe latest `attrs` ke saath side effects perform kar sako.

</div>

<div class="options-api">

Agar zarurat ho, to aap `$attrs` instance property ke through fallthrough attributes ko access kar sakte ho:

```js
export default {
  created() {
    console.log(this.$attrs)
  }
}
```

</div>
