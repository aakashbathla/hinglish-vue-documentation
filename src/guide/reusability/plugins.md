# Plugins {#plugins}

## Introduction {#introduction}

Plugins self-contained code hote hain jo usually Vue me app-level functionality add karte hain. Aise hum ek plugin install karte hain:

```js
import { createApp } from 'vue'

const app = createApp({})

app.use(myPlugin, {
  /* optional options */
})
```

Ek plugin ya to ek object hota hai jisme `install()` method hota hai, ya phir ek simple function jo install function ki tarah kaam karta hai. Install function ko [app instance](/api/application) milta hai saath hi wo additional options bhi jo `app.use()` ke through pass kiye jaate hain (agar diye gaye ho):

```js
const myPlugin = {
  install(app, options) {
    // configure the app
  }
}
```

Plugin ke liye koi strictly defined scope nahi hota, lekin common scenarios jaha plugins useful hote hain, unme shamil hain:

1. Ek ya zyada global components ya custom directives register karna [`app.component()`](/api/application#app-component) aur [`app.directive()`](/api/application#app-directive) ke through.

2. Ek resource ko poore app me [injectable](/guide/components/provide-inject) banana [`app.provide()`](/api/application#app-provide) call karke.

3. Kuch global instance properties ya methods add karna, unhe [`app.config.globalProperties`](/api/application#app-config-globalproperties) me attach karke.

4. Ek library jo inme se ek combination perform karti hai (jaise [vue-router](https://github.com/vuejs/vue-router-next)).

## Writing a Plugin {#writing-a-plugin}

Apna khud ka Vue.js plugin banane ko acche se samajhne ke liye, hum ek simplified version banayenge jo `i18n` (short for [Internationalization](https://en.wikipedia.org/wiki/Internationalization_and_localization)) strings display karega.

Sabse pehle hum plugin object setup karte hain. Ye recommend kiya jata hai ki aap isse ek separate file me create aur export karo, taaki logic alag aur organized rahe.

```js [plugins/i18n.js]
export default {
  install: (app, options) => {
    // Plugin code goes here
  }
}
```

Hum ek translation function banana chahte hain. Ye function ek dot-delimited `key` string lega, jiska use hum user-provided options me se translated string lookup karne ke liye karenge. Ye hai intended usage templates me:

```vue-html
<h1>{{ $translate('greetings.hello') }}</h1>
```

Kyuki ye function sabhi templates me globally available hona chahiye, hum ise apne plugin me app.config.globalProperties ke sath attach karke globally available karenge:

```js{4-11}
// plugins/i18n.js
export default {
  install: (app, options) => {
    // inject a globally available $translate() method
    app.config.globalProperties.$translate = (key) => {
      // retrieve a nested property in `options`
      // using `key` as the path
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, options)
    }
  }
}
```

Hamari `$translate` function ek string lega jaise `greetings.hello`, fir user-provided configuration ke andar dekhega aur uska translated value return karega.

Jo object translated keys contain karta hai, use plugin ko install karte time `app.use()` ke additional parameters ke through pass karna chahiye:

```js
import i18nPlugin from './plugins/i18n'

app.use(i18nPlugin, {
  greetings: {
    hello: 'Bonjour!'
  }
})
```

Ab, hamara initial expression `$translate('greetings.hello')` runtime pe `Bonjour!` se replace ho jayega.

Aur dekho: [Augmenting Global Properties](/guide/typescript/options-api#augmenting-global-properties) <sup class="vt-badge ts" />

:::tip
Global properties ko bahut kam use karo, kyunki agar bahut zyada global properties alag-alag plugins ke through inject ho jayein to app me confuse ho sakta hai.
:::

### Provide / Inject with Plugins {#provide-inject-with-plugins}

Plugins hume `provide` use karne ka option bhi dete hain jisse hum plugin users ko koi function ya attribute access kara sakte hain.  
For example, hum application ko `options` parameter provide kar sakte hain taaki wo translations object ko use kar sake.

```js{10} [plugins/i18n.js]
export default {
  install: (app, options) => {
    app.provide('i18n', options)
  }
}
```

Ab plugin users apne components me `i18n` key use karke plugin options ko inject kar paayenge:

<div class="composition-api">

```vue
<script setup>
import { inject } from 'vue'

const i18n = inject('i18n')

console.log(i18n.greetings.hello)
</script>
```

</div>
<div class="options-api">

```js
export default {
  inject: ['i18n'],
  created() {
    console.log(this.i18n.greetings.hello)
  }
}
```

</div>

### NPM ke liye Bundle

Agar aap apne plugin ko aur aage build karke dusre logon ke use ke liye publish karna chahte ho, toh [Vite ki Library Mode wali section](https://vitejs.dev/guide/build.html#library-mode) dekho.
