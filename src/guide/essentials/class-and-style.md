# Class aur Style Bindings {#class-and-style-bindings}

Ek common need hoti hai data binding mein kisi element ke class list aur inline styles ko manipulate karna. Kyuki `class` aur `style` dono attributes hain, hum inhe `v-bind` ke through dynamically string value assign kar sakte hain, jaise ki dusre attributes ke saath karte hain. Lekin agar hum string concatenation ka use karein in values ko generate karne ke liye, toh wo kaafi annoying aur galtiyon se bhara ho sakta hai. Is wajah se, Vue `v-bind` ke saath `class` aur `style` pe kuch special enhancements provide karta hai. Strings ke alawa, expressions objects ya arrays ko bhi evaluate kar sakte hain.

## Binding HTML Classes {#binding-html-classes}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/dynamic-css-classes-with-vue-3" title="Free Vue.js Dynamic CSS Classes Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-dynamic-css-classes-with-vue" title="Free Vue.js Dynamic CSS Classes Lesson"/>
</div>

### Objects ke saath binding {#binding-to-objects}

Hum `:class` (jo `v-bind:class` ka shortcut hai) ko ek object pass kar sakte hain taaki dynamically classes toggle kar sakein:

```vue-html
<div :class="{ active: isActive }"></div>
```

Iska matlab hai ki `active` class tabhi apply hogi jab `isActive` ka value truthy hoga.

Agar aapko multiple classes toggle karni ho, toh object mein multiple fields daal sakte ho. Saath hi, `:class` directive normal `class` attribute ke saath bhi use ho sakta hai. Jaise ki agar humare paas ye state ho:

<div class="composition-api">

```js
const isActive = ref(true)
const hasError = ref(false)
```

</div>

<div class="options-api">

```js
data() {
  return {
    isActive: true,
    hasError: false
  }
}
```

</div>

Aur template mein likha ho:

```vue-html
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```

Toh render hoga:

```vue-html
<div class="static active"></div>
```

Agar `hasError` ka value `true` ho gaya, toh class list ban jaayegi `"static active text-danger"`.

Ye object inline hone ki zarurat nahi hai:

<div class="composition-api">

```js
const classObject = reactive({
  active: true,
  'text-danger': false
})
```

</div>

<div class="options-api">

```js
data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}
```

</div>

```vue-html
<div :class="classObject"></div>
```

Ye render karega:

```vue-html
<div class="active"></div>
```

Hum ek [computed property](./computed) bhi use kar sakte hain jo object return kare — ye kaafi common aur powerful pattern hai:

<div class="composition-api">

```js
const isActive = ref(true)
const error = ref(null)

const classObject = computed(() => ({
  active: isActive.value && !error.value,
  'text-danger': error.value && error.value.type === 'fatal'
}))
```

</div>

<div class="options-api">

```js
data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

</div>

```vue-html
<div :class="classObject"></div>
```

### Arrays ke saath binding {#binding-to-arrays}

Hum `:class` ko ek array se bind kar sakte hain taaki ek list of classes apply ho:

<div class="composition-api">

```js
const activeClass = ref('active')
const errorClass = ref('text-danger')
```

</div>

<div class="options-api">

```js
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

</div>

```vue-html
<div :class="[activeClass, errorClass]"></div>
```

Jo render karega:

```vue-html
<div class="active text-danger"></div>
```

Agar aap kisi class ko conditionally apply karna chahte ho, toh ternary expression use kar sakte ho:

```vue-html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

Yahaan `errorClass` hamesha apply hogi, lekin `activeClass` tabhi apply hogi jab `isActive` truthy ho.

Agar multiple conditional classes ho, toh ye kaafi verbose ho sakta hai. Isliye hum array ke andar object syntax bhi use kar sakte hain:

```vue-html
<div :class="[{ [activeClass]: isActive }, errorClass]"></div>
```

### Components ke saath {#with-components}

> Ye section maan ke chalta hai ki aapko [Components](/guide/essentials/component-basics) ki knowledge hai. Agar nahi, toh abhi skip kar sakte ho aur baad mein wapas aa sakte ho.

Jab aap `class` attribute kisi component pe use karte ho jiska ek single root element hai, toh wo classes us root element mein merge ho jaati hain.

Jaise agar `MyComponent` naam ka component hai jiska template hai:

```vue-html
<!-- child component template -->
<p class="foo bar">Hi!</p>
```

Aur jab aap use use karte ho:

```vue-html
<!-- jab component use karte ho -->
<MyComponent class="baz boo" />
```

Toh render hoga:

```vue-html
<p class="foo bar baz boo">Hi!</p>
```

Same cheez `class` bindings ke liye bhi kaam karti hai:

```vue-html
<MyComponent :class="{ active: isActive }" />
```

Agar `isActive` truthy hai, toh render hoga:

```vue-html
<p class="foo bar active">Hi!</p>
```

Agar aapka component ke paas multiple root elements hain, toh aapko batana padega ki kaun sa element ye class lega. Ye `\$attrs` property ka use karke hota hai:

```vue-html
<!-- MyComponent template using $attrs -->
<p :class="$attrs.class">Hi!</p>
<span>This is a child component</span>
```

```vue-html
<MyComponent class="baz" />
```

Render karega:

```html
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

Aap component ke attribute inheritance ke baare mein aur jaan sakte hain [Fallthrough Attributes](/guide/components/attrs) section mein.

## Binding Inline Styles {#binding-inline-styles}

### Objects ke saath binding {#binding-to-objects-1}

`:style` directive JavaScript object values ko bind kar sakta hai — ye HTML element ke [`style` property](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/style) ke barabar hota hai:

<div class="composition-api">

```js
const activeColor = ref('red')
const fontSize = ref(30)
```

</div>

<div class="options-api">

```js
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

</div>

```vue-html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

CamelCase keys preferred hain, lekin `:style` kebab-case CSS properties ko bhi support karta hai — jaise ki:

```vue-html
<div :style="{ 'font-size': fontSize + 'px' }"></div>
```

Template ko cleaner banane ke liye, aap ek style object ko directly bind kar sakte ho:

<div class="composition-api">

```js
const styleObject = reactive({
  color: 'red',
  fontSize: '30px'
})
```

</div>

<div class="options-api">

```js
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

</div>

```vue-html
<div :style="styleObject"></div>
```

Yeh bhi common hai ki style binding ko computed properties ke saath use kiya jaye jo objects return karti hain.

`:style` directives normal `style` attributes ke saath bhi kaam karti hain:

```vue-html
<h1 style="color: red" :style="'font-size: 1em'">hello</h1>
```

Iska rendered output hoga:

```vue-html
<h1 style="color: red; font-size: 1em;">hello</h1>
```

### Arrays ke saath binding {#binding-to-arrays-1}

Hum `:style` ko multiple style objects ke array ke saath bind kar sakte hain. Ye sabhi objects merge ho jaate hain aur element pe apply hote hain:

```vue-html
<div :style="[baseStyles, overridingStyles]"></div>
```

### Auto-prefixing {#auto-prefixing}

Agar aap kisi CSS property ka use karte ho jisko [vendor prefix](https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix) chahiye, Vue automatically appropriate prefix laga deta hai. Vue runtime mein check karta hai ki kaunse properties browser support karta hai. Agar browser kisi property ko support nahi karta, toh Vue prefixed versions try karta hai.

### Multiple Values {#multiple-values}

Kisi style property ke liye aap multiple (prefixed) values ki array provide kar sakte ho:

```vue-html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Browser us list mein se jo last value support karega, sirf wahi render hogi. Is example mein, agar browser `flex` support karta hai, toh `display: flex` render hoga.
