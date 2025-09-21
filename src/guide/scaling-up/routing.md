# Routing {#routing}

## Client-Side vs. Server-Side Routing {#client-side-vs-server-side-routing}

Server side routing ka matlab hai ki server ek response bhejta hai based on URL path jo user visit kar raha hai. Jab hum ek traditional server-rendered web app mein link par click karte hain, toh browser ko server se ek HTML response milta hai aur pura page reload hota hai naye HTML ke saath.

Lekin ek [Single-Page Application](https://developer.mozilla.org/en-US/docs/Glossary/SPA) (SPA) mein, client-side JavaScript navigation ko intercept kar sakti hai, dynamically naya data fetch karke current page ko update kar sakti hai bina full page reload ke. Ye approach zyada fast aur smooth user experience deti hai, specially un cases mein jo actual "applications" jaise hote hain, jahan user se expect hota hai ki wo lambe time tak multiple interactions kare.

Aise SPAs mein, "routing" client side par hoti hai, yani browser ke andar. Client-side router responsible hota hai application ke rendered view ko manage karne ke liye, browser APIs jaise [History API](https://developer.mozilla.org/en-US/docs/Web/API/History) ya [`hashchange` event](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event) ka use karke.

## Official Router {#official-router}

<!-- TODO update links -->
<div>
  <VueSchoolLink href="https://vueschool.io/courses/vue-router-4-for-everyone" title="Free Vue Router Course">
    Watch a Free Video Course on Vue School
  </VueSchoolLink>
</div>

Vue SPAs banane ke liye bahut suitable hai. Zyada tar SPAs ke liye, officially-supported [Vue Router library](https://github.com/vuejs/router) use karne ki recommendation hoti hai. Aur details ke liye dekhein Vue Router ki [documentation](https://router.vuejs.org/).

## Simple Routing from Scratch {#simple-routing-from-scratch}

Agar aapko sirf basic routing ki zarurat hai aur ek full-featured router library use nahi karna chahte, toh aap [Dynamic Components](/guide/essentials/component-basics#dynamic-components) ka use karke aur browser ke [`hashchange` events](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event) ya [History API](https://developer.mozilla.org/en-US/docs/Web/API/History) ko listen karke current component state update kar sakte ho.

Ye ek simple example hai:

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})
</script>

<template>
  <a href="#/">Home</a> | <a href="#/about">About</a> |
  <a href="#/non-existent-path">Broken Link</a>
  <component :is="currentView" />
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNptUk1vgkAQ/SsTegAThZp4MmhikzY9mKanXkoPWxjLRpgly6JN1P/eWb5Eywlm572ZN2/m5GyKwj9U6CydsIy1LAyUaKpiHZHMC6UNnEDjbgqxyovKYAIX2GmVg8sktwe9qhzbdz+wga15TW++VWX6fB3dAt6UeVEVJT2me2hhEcWKSgOamVjCCk4RAbiBu6xbT5tI2ML8VDeI6HLlxZXWSOZdmJTJPJB3lJSoo5+pWBipyE9FmU4soU2IJHk+MGUrS4OE2nMtIk4F/aA7BW8Cq3WjYlDbP4isQu4wVp0F1Q1uFH1IPDK+c9cb1NW8B03tyJ//uvhlJmP05hM4n60TX/bb2db0CoNmpbxMDgzmRSYMcgQQCkjZhlXkPASRs7YmhoFYw/k+WXvKiNrTcQgpmuFv7ZOZFSyQ4U9a7ZFgK2lvSTXFDqmIQbCUJTMHFkQOBAwKg16kM3W6O7K3eSs+nbeK+eee1V/XKK0dY4Q3vLhR6uJxMUK8/AFKaB6k)

</div>

<div class="options-api">

```vue
<script>
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

export default {
  data() {
    return {
      currentPath: window.location.hash
    }
  },
  computed: {
    currentView() {
      return routes[this.currentPath.slice(1) || '/'] || NotFound
    }
  },
  mounted() {
    window.addEventListener('hashchange', () => {
      this.currentPath = window.location.hash
    })
  }
}
</script>

<template>
  <a href="#/">Home</a> | <a href="#/about">About</a> |
  <a href="#/non-existent-path">Broken Link</a>
  <component :is="currentView" />
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNptUstO6zAQ/ZVR7iKtVJKLxCpKK3Gli1ggxIoNZmGSKbFoxpEzoUi0/87YeVBKNonHPmfOmcdndN00yXuHURblbeFMwxtFpm6sY7i1NcLW2RriJPWBB8bT8/WL7Xh6D9FPwL3lG9tROWHGiwGmqLDUMjhhYgtr+FQEEKdxFqRXfaR9YrkKAoqOnocfQaDEre523PNKzXqx7M8ADrlzNEYAReccEj9orjLYGyrtPtnZQrOxlFS6rXqgZJdPUC5s3YivMhuTDCkeDe6/dSalvognrkybnIgl7c4UuLhcwuHgS3v2/7EPvzRruRXJ7/SDU12W/98l451pGQndIvaWi0rTK8YrEPx64ymKFQOce5DOzlfs4cdlkA+NzdNpBSRgrJudZpQIINdQOdyuVfQnVdHGzydP9QYO549hXIII45qHkKUL/Ail8EUjBgX+z9k3JLgz9OZJgeInYElAkJlWmCcDUBGkAsrTyWS0isYV9bv803x1OTiWwzlrWtxZ2lDGDO90mWepV3+vZojHL3QQKQE=)

</div>
