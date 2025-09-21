<script setup>
import { VTCodeGroup, VTCodeGroupTab } from '@vue/theme'
</script>

# Tooling {#tooling}

## Try It Online {#try-it-online}

Vue SFCs ko try karne ke liye aapko apne machine pe kuch install karne ki zaroorat nahi hai — kuch online playgrounds hain jahan aap directly browser me try kar sakte ho:

- [Vue SFC Playground](https://play.vuejs.org)
  - Hamesha latest commit se deployed
  - Component compilation results inspect karne ke liye design kiya gaya hai
- [Vue + Vite on StackBlitz](https://vite.new/vue)
  - IDE-like environment jisme actual Vite dev server browser me run hota hai
  - Local setup ke sabse kareeb

Ye online playgrounds bug report karte waqt reproductions provide karne ke liye bhi recommend kiye jate hain.

## Project Scaffolding {#project-scaffolding}

### Vite {#vite}

[Vite](https://vitejs.dev/) ek lightweight aur fast build tool hai jisme Vue SFC ke liye first-class support milta hai. Isse Evan You ne banaya hai, jo Vue ke author bhi hain!

Vite + Vue ke sath start karne ke liye, bas ye command run karo:

::: code-group

```sh [npm]
$ npm create vue@latest
```

```sh [pnpm]
$ pnpm create vue@latest
```

```sh [yarn]
# For Yarn Modern (v2+)
$ yarn create vue@latest

# For Yarn ^v4.11
$ yarn dlx create-vue@latest
```

```sh [bun]
$ bun create vue@latest
```

:::

Ye command [create-vue](https://github.com/vuejs/create-vue) ko install aur execute karega, jo Vue ka official project scaffolding tool hai.

- Vite ke baare me aur seekhne ke liye [Vite docs](https://vitejs.dev) check karo.
- Vue-specific behavior ko configure karne ke liye, jaise Vue compiler ko options pass karna, [@vitejs/plugin-vue](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#readme) docs check karo.

Upar bataye gaye dono online playgrounds files ko Vite project ke roop me download karne ka option bhi dete hain.

### Vue CLI {#vue-cli}

[Vue CLI](https://cli.vuejs.org/) Vue ke liye official webpack-based toolchain hai. Ab ye maintenance mode me hai, aur naye projects ke liye hum recommend karte hain ki aap Vite use karo, jab tak aapko specifically webpack-only features ki zaroorat na ho. Zyada cases me Vite better developer experience deta hai.

Vue CLI se Vite me migrate karne ke liye:

- [Vue CLI -> Vite Migration Guide from VueSchool.io](https://vueschool.io/articles/vuejs-tutorials/how-to-migrate-from-vue-cli-to-vite/)
- [Auto migration ke liye tools / plugins](https://github.com/vitejs/awesome-vite#vue-cli)

### Note on In-Browser Template Compilation {#note-on-in-browser-template-compilation}

Jab Vue bina build step ke use hota hai, tab component templates ya to directly page ke HTML me likhe jaate hain ya inline JavaScript strings ke roop me. Aise cases me Vue ko template compiler browser me ship karna padta hai taaki wo on-the-fly compilation kar sake. Agar hum pehle hi templates ko build step me compile kar lein, to compiler unnecessary ho jata hai. Client bundle size kam karne ke liye Vue [different "builds"](https://unpkg.com/browse/vue@3/dist/) provide karta hai:

- `vue.runtime.*` se start hone wale build files **runtime-only builds** hote hain: inme compiler include nahi hota. Is case me saare templates ko build step ke through pre-compile karna padta hai.
- Jo build files `.runtime` include nahi karte, wo **full builds** hote hain: inme compiler hota hai aur browser me directly template compile karne ka support hota hai. Lekin payload ~14kb zyada ho jata hai.

Default tooling setups runtime-only build use karte hain, kyunki SFCs ke saare templates pre-compiled hote hain. Agar kisi reason se aapko build step ke sath bhi in-browser template compilation chahiye, to aap build tool ko configure karke `vue` ko `vue/dist/vue.esm-bundler.js` par alias kar sakte ho.

Agar aapko ek lightweight alternative chahiye bina build-step usage ke liye, to [petite-vue](https://github.com/vuejs/petite-vue) check karo.

## IDE Support {#ide-support}

- Recommended IDE setup hai [VS Code](https://code.visualstudio.com/) + [Vue - Official extension](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (pehle Volar). Ye extension syntax highlighting, TypeScript support, aur template expressions & component props ke liye intellisense provide karta hai.

  :::tip
  Vue - Official extension, [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur) ko replace karta hai (jo Vue 2 ke liye tha). Agar aapke paas Vetur install hai, to Vue 3 projects me usse disable kar lena.  
  :::

- [WebStorm](https://www.jetbrains.com/webstorm/) me bhi Vue SFCs ke liye great built-in support hai.

- Baaki IDEs jo [Language Service Protocol](https://microsoft.github.io/language-server-protocol/) (LSP) support karte hain, wo bhi Volar ki functionalities LSP ke through use kar sakte hain:
  - Sublime Text: [LSP-Volar](https://github.com/sublimelsp/LSP-volar)
  - vim / Neovim: [coc-volar](https://github.com/yaegassy/coc-volar)
  - emacs: [lsp-mode](https://emacs-lsp.github.io/lsp-mode/page/lsp-volar/)

## Browser Devtools {#browser-devtools}

Vue browser devtools extension aapko Vue app ka component tree explore karne, individual components ka state inspect karne, state management events track karne aur performance profile karne deta hai.

![devtools screenshot](./images/devtools.png)

- [Documentation](https://devtools.vuejs.org/)
- [Chrome Extension](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
- [Vite Plugin](https://devtools.vuejs.org/guide/vite-plugin)
- [Standalone Electron app](https://devtools.vuejs.org/guide/standalone)

## TypeScript {#typescript}

Main article: [Using Vue with TypeScript](/guide/typescript/overview).

- [Vue - Official extension](https://github.com/vuejs/language-tools) `<script lang="ts">` blocks ke liye type checking provide karta hai, jisme template expressions aur cross-component props validation bhi included hai.
- Command line se type checking karne ke liye ya `d.ts` files generate karne ke liye [`vue-tsc`](https://github.com/vuejs/language-tools/tree/master/packages/tsc) use karo.

## Testing {#testing}

Main article: [Testing Guide](/guide/scaling-up/testing).

- [Cypress](https://www.cypress.io/) E2E tests ke liye recommended hai. Ye Vue SFCs ke component testing ke liye bhi use ho sakta hai [Cypress Component Test Runner](https://docs.cypress.io/guides/component-testing/introduction) ke sath.
- [Vitest](https://vitest.dev/) ek fast test runner hai jo Vue / Vite team members ne banaya hai. Ye Vite-based apps ke liye specifically design hua hai taki same instant feedback loop mile unit / component testing me.
- [Jest](https://jestjs.io/) ko [vite-jest](https://github.com/sodatea/vite-jest) ke through Vite ke sath use kiya ja sakta hai. Lekin ye sirf tab recommend hai jab aapke paas pehle se Jest-based test suites ho jo migrate karne hain, kyunki Vitest already similar functionality better integration ke sath provide karta hai.

## Linting {#linting}

Vue team maintain karti hai [eslint-plugin-vue](https://github.com/vuejs/eslint-plugin-vue), jo SFC-specific linting rules support karta hai.

Pehle Vue CLI use karne wale users shayad webpack loaders ke sath linters configure karne ke aadat wale ho. Lekin Vite-based setup me recommendation ye hai:

1. `npm install -D eslint eslint-plugin-vue` run karo, aur fir `eslint-plugin-vue` ka [configuration guide](https://eslint.vuejs.org/user-guide/#usage) follow karo.
2. ESLint IDE extensions setup karo, jaise [ESLint for VS Code](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint), taki development ke dauraan editor me hi lint feedback mile. Isse dev server start karte waqt unnecessary linting cost bacha sakte ho.
3. ESLint ko production build command ke part ke roop me run karo, taki production me ship karne se pehle full lint feedback mile.
4. (Optional) [lint-staged](https://github.com/okonet/lint-staged) setup karo taaki git commit pe modified files automatically lint ho jayein.

## Formatting {#formatting}

- [Vue - Official](https://github.com/vuejs/language-tools) VS Code extension out of the box Vue SFCs ke liye formatting support karta hai.
- [Prettier](https://prettier.io/) me bhi Vue SFC formatting support built-in hai.

## SFC Custom Block Integrations {#sfc-custom-block-integrations}

Custom blocks ko same Vue file me alag request queries ke sath imports ke roop me compile kiya jata hai. Ye underlying build tool par depend karta hai ki wo in imports ko kaise handle kare.

- Vite use karte waqt, ek custom Vite plugin use karna chahiye jo matched custom blocks ko executable JavaScript me transform kare. [Example](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#example-for-transforming-custom-blocks)
- Vue CLI ya plain webpack use karte waqt, ek webpack loader configure karna hota hai taaki matched blocks transform ho jayein. [Example](https://vue-loader.vuejs.org/guide/custom-blocks.html)

## Lower-Level Packages {#lower-level-packages}

### `@vue/compiler-sfc` {#vue-compiler-sfc}

- [Docs](https://github.com/vuejs/core/tree/main/packages/compiler-sfc)

Ye package Vue core monorepo ka part hai aur hamesha main `vue` package ke same version me publish hota hai. Ye `vue/compiler-sfc` ke under proxy kiya gaya hai, isliye alag se install karne ki zaroorat nahi hai.

Ye package low-level utilities provide karta hai Vue SFCs process karne ke liye, aur ye mainly tooling authors ke liye hai jo custom tools me Vue SFC support chahte hain.

:::tip
Hamesha is package ko `vue/compiler-sfc` deep import ke roop me use karo taki iska version Vue runtime ke sath sync me rahe.  
:::

### `@vitejs/plugin-vue` {#vitejs-plugin-vue}

- [Docs](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue)

Ye official plugin hai jo Vite me Vue SFC support provide karta hai.

### `vue-loader` {#vue-loader}

- [Docs](https://vue-loader.vuejs.org/)

Ye official loader hai jo webpack me Vue SFC support provide karta hai. Agar aap Vue CLI use kar rahe ho, to [Vue CLI me vue-loader options modify karne ke docs](https://cli.vuejs.org/guide/webpack.html#modifying-options-of-a-loader) bhi check karo.

## Other Online Playgrounds {#other-online-playgrounds}

- [VueUse Playground](https://play.vueuse.org)
- [Vue + Vite on Repl.it](https://replit.com/@templates/VueJS-with-Vite)
- [Vue on CodeSandbox](https://codesandbox.io/p/devbox/github/codesandbox/sandbox-templates/tree/main/vue-vite)
- [Vue on Codepen](https://codepen.io/pen/editor/vue)
- [Vue on WebComponents.dev](https://webcomponents.dev/create/cevue)

<!-- TODO ## Backend Framework Integrations -->
