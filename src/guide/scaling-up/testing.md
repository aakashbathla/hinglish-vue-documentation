<script setup>
import { VTCodeGroup, VTCodeGroupTab } from '@vue/theme'
</script>
<style>
.lambdatest {
  background-color: var(--vt-c-bg-soft);
  border-radius: 8px;
  padding: 12px 16px 12px 12px;
  font-size: 13px;
  a {
    display: flex;
    color: var(--vt-c-text-2);
  }
  img {
    background-color: #fff;
    padding: 12px 16px;
    border-radius: 6px;
    margin-right: 24px;
  }
  .testing-partner {
    color: var(--vt-c-text-1);
    font-size: 15px;
    font-weight: 600;
  }
}
</style>

# Testing {#testing}

## Why Test? {#why-test}

Automated tests aap aur aapki team ko complex Vue applications ko jaldi aur confidently build karne me madad karte hain. Ye regressions ko prevent karte hain aur aapko encourage karte hain ki aap apne application ko chhoti-chhoti testable functions, modules, classes aur components me tod dein. Jaise har application ke saath hota hai, aapka naya Vue app bhi alag-alag tareeke se break ho sakta hai, aur ye zaroori hai ki aap un issues ko release karne se pehle catch aur fix kar sako.

Is guide me hum basic terminology cover karenge aur recommend karenge ki kaunse tools aapke Vue 3 application ke liye best rahenge.

Ek section Vue-specific hai jo composables cover karta hai. Details ke liye dekhein [Testing Composables](#testing-composables).

## When to Test {#when-to-test}

Testing jaldi shuru karo! Hum recommend karte hain ki jaise hi possible ho, tests likhna start kar do. Jitna late karoge tests add karne me, utni zyada dependencies aapke app me ho jayengi, aur utna hi mushkil hoga start karna.

## Testing Types {#testing-types}

Jab aap apne Vue app ka testing strategy design kar rahe ho, toh aapko in testing types ka use karna chahiye:

- **Unit**: Ye check karta hai ki diya gaya function, class ya composable ke inputs expected output ya side effects produce kar rahe hain ya nahi.
- **Component**: Ye check karta hai ki aapka component mount hota hai, render hota hai, interact kiya ja sakta hai, aur expected tarike se behave karta hai. Ye tests unit tests se zyada code import karte hain, zyada complex hote hain aur execute hone me zyada time lete hain.
- **End-to-end**: Ye un features ko check karta hai jo multiple pages tak spread hote hain aur real network requests karte hain aapke production-built Vue app ke against. Ye tests aksar database ya koi backend bhi involve karte hain.

Har testing type ka apna role hota hai aapke app ke testing strategy me, aur har ek alag-alag types ke issues se aapko protect karta hai.

## Overview {#overview}

Hum chhote se discuss karenge ki ye sab kya hain, Vue apps ke liye kaise implement kiye ja sakte hain, aur kuch general recommendations denge.

## Unit Testing {#unit-testing}

Unit tests likhe jaate hain ye verify karne ke liye ki chhote, isolated code units expected tarike se kaam kar rahe hain. Ek unit test usually ek single function, class, composable ya module cover karta hai. Unit tests logical correctness pe focus karte hain aur sirf application ke ek chhote part ki functionality se concern hote hain. Ye aapke app ke environment ke bade parts ko mock kar sakte hain (jaise initial state, complex classes, 3rd party modules, aur network requests).

Generally, unit tests ek function ke business logic aur logical correctness me issues catch karenge.

Example ke liye ek `increment` function dekho:

```js [helpers.js]
export function increment(current, max = 10) {
  if (current < max) {
    return current + 1
  }
  return current
}
```

Kyuki ye function bahut hi self-contained hai, isliye `increment` function ko invoke karna aur assert karna easy hoga ki ye wahi return kar raha hai jo expected hai. Isliye hum iske liye ek Unit Test likhenge.

Agar inme se koi bhi assertion fail ho jata hai, toh clearly issue sirf `increment` function ke andar hi hoga.

```js{4-16} [helpers.spec.js]
import { increment } from './helpers'

describe('increment', () => {
  test('increments the current number by 1', () => {
    expect(increment(0, 10)).toBe(1)
  })

  test('does not increment the current number over the max', () => {
    expect(increment(10, 10)).toBe(10)
  })

  test('has a default max of 10', () => {
    expect(increment(10)).toBe(10)
  })
})
```

Jaise pehle mention kiya gaya tha, unit testing generally un self-contained business logic, components, classes, modules ya functions pe apply hoti hai jo UI rendering, network requests ya environment concerns se deal nahi karte.

Ye mostly plain JavaScript / TypeScript modules hote hain jo Vue se unrelated hote hain. General rule ye hai ki Vue applications ke business logic ka unit testing likhna dusre frameworks jaise hi hota hai.

Do cases hote hain jaha aapko Vue-specific features ka unit test likhna padta hai:

1. **Composables**
2. **Components**

---

### Composables {#composables}

Vue applications ka ek specific function category hai [Composables](/guide/reusability/composables), jinhe test karte waqt special handling ki zarurat ho sakti hai.  
Details ke liye dekho [Testing Composables](#testing-composables).

---

### Unit Testing Components {#unit-testing-components}

Ek component ko test karne ke do tareeke hote hain:

1. **Whitebox: Unit Testing**  
   Whitebox tests component ke implementation details aur dependencies ko jaante hain. Focus hota hai **component ko isolate** karne par.  
   Inme aksar aapko child components mock karne padenge, aur plugins / dependencies (jaise Pinia) ka setup karna padega.

2. **Blackbox: Component Testing**  
   Blackbox tests component ke implementation details ko nahi jaante. Ye kam se kam mocking use karte hain taaki component aur poore system ka integration test ho sake. Ye usually sabhi child components render karte hain aur zyada "integration test" ke category me aate hain.  
   Dekho [Component Testing recommendations](#component-testing).

---

### Recommendation {#recommendation}

- [Vitest](https://vitest.dev/)  
  Kyunki `create-vue` ke official setup me [Vite](https://vitejs.dev/) use hota hai, isliye recommend kiya jata hai ki aap unit testing framework bhi wahi use karo jo Vite ke config aur pipeline ke sath seamlessly integrate ho.  
  [Vitest](https://vitest.dev/) isi purpose ke liye design hua hai, Vue / Vite team members ne banaya aur maintain kiya hai. Ye Vite-based projects ke sath minimal effort me integrate ho jata hai aur bahut fast hai.

---

### Other Options {#other-options}

- [Jest](https://jestjs.io/) ek popular unit testing framework hai. Lekin recommend tabhi hai agar aapke paas already Jest test suite ho jo aapko migrate karna ho Vite-based project me. Naye projects ke liye **Vitest** better performance aur seamless integration deta hai.

---

## Component Testing {#component-testing}

Vue applications me **components** UI ke main building blocks hote hain. Isliye components naturally best candidate hain isolation aur testing ke liye. Granularity ke perspective se, component testing unit testing se ek level upar hota hai aur integration testing ke form me count hota hai.  
Aapke Vue Application ka zyada hissa component tests se cover hona chahiye aur recommend hai ki har Vue component ka apna ek spec file ho.

Component tests ko issues pakadne chahiye jo component ke **props, events, slots, styles, classes, lifecycle hooks** etc. se related ho.

Component tests me child components ko mock nahi karna chahiye, balki component aur uske children ke beech interaction ko test karna chahiye jaise ek user karta hai.  
For example, ek component test ko element pe click karna chahiye jaise ek user karta hai, instead of programmatically component se interact karne ke.

Component tests ka focus component ke **public interfaces** pe hona chahiye, internal implementation details pe nahi.  
Most components ke liye public interface limited hota hai: **events emitted, props, aur slots**.  
Testing karte waqt hamesha yaad rakho: **component kya karta hai test karo, kaise karta hai wo nahi**.

---

**DO**

- **Visual logic ke liye**: assert karo ki given props aur slots ke base pe sahi render output ho.
- **Behavioral logic ke liye**: assert karo ki user input events ke response me sahi render updates ya events emit ho.

Example: ek Stepper component hai jisme ek DOM element "increment" label ke sath hai aur uspe click kiya ja sakta hai.  
Hum ek `max` prop pass karte hain jo Stepper ko 2 se aage increment hone se rokta hai. Agar hum button ko 3 baar click karte hain, toh UI me fir bhi `2` hi dikhna chahiye.

Yaha hume Stepper ke implementation ke baare me kuch nahi pata — sirf ye ki input hai `max` prop aur output hai DOM ka state jaise user ko dikhega.

::: code-group

```js [Vue Test Utils]
const valueSelector = '[data-testid=stepper-value]'
const buttonSelector = '[data-testid=increment]'

const wrapper = mount(Stepper, {
  props: {
    max: 1
  }
})

expect(wrapper.find(valueSelector).text()).toContain('0')

await wrapper.find(buttonSelector).trigger('click')

expect(wrapper.find(valueSelector).text()).toContain('1')
```

```js [Cypress]
const valueSelector = '[data-testid=stepper-value]'
const buttonSelector = '[data-testid=increment]'

mount(Stepper, {
  props: {
    max: 1
  }
})

cy.get(valueSelector)
  .should('be.visible')
  .and('contain.text', '0')
  .get(buttonSelector)
  .click()
  .get(valueSelector)
  .should('contain.text', '1')
```

```js [Testing Library]
const { getByText } = render(Stepper, {
  props: {
    max: 1
  }
})

getByText('0') // Implicit assertion that "0" is within the component

const button = getByRole('button', { name: /increment/i })

// Dispatch a click event to our increment button.
await fireEvent.click(button)

getByText('1')

await fireEvent.click(button)
```

:::

**DON'T**

- Component instance ka private state assert mat karo ya uske private methods ko test mat karo. Implementation details test karne se tests brittle ho jaate hain, kyunki agar implementation change hoti hai toh tests bhi break ho jaate hain aur update karne padte hain.

  Component ka ultimate kaam hai sahi DOM output render karna. Isliye DOM output par focus karne wale tests wahi level ka correctness assurance dete hain (agar zyada nahi), aur zyada robust hote hain aur changes ke against resilient hote hain.

  Sirf snapshot tests pe rely mat karo. HTML strings ko assert karna correctness describe nahi karta. Hamesha intentional tests likho.

  Agar ek method ko thoroughly test karna zaroori hai, toh usse standalone utility function me extract karne ka socho aur uske liye dedicated unit test likho. Agar extract cleanly nahi ho raha, toh use component, integration, ya end-to-end test ke part ke roop me test karo jo usse cover karta ho.

---

### Recommendation {#recommendation-1}

- [Vitest](https://vitest.dev/) un components ya composables ke liye jo headlessly render hote hain (jaise [`useFavicon`](https://vueuse.org/core/useFavicon/#usefavicon) function VueUse me). Components aur DOM ko test karne ke liye [`@vue/test-utils`](https://github.com/vuejs/test-utils) use kar sakte ho.

- [Cypress Component Testing](https://on.cypress.io/component) un components ke liye jinka expected behavior styles ya native DOM events pe depend karta hai. Ye Testing Library ke sath bhi use ho sakta hai via [@testing-library/cypress](https://testing-library.com/docs/cypress-testing-library/intro).

Vitest aur browser-based runners ke beech main differences hain **speed** aur **execution context**. Browser-based runners (jaise Cypress) un issues ko catch kar sakte hain jo node-based runners (jaise Vitest) nahi kar paate (jaise style issues, real native DOM events, cookies, local storage, aur network failures). Lekin ye Vitest se kai guna slow hote hain kyunki browser open karte hain, stylesheets compile karte hain, aur aur bhi.  
Cypress ek browser-based runner hai jo component testing support karta hai. Vitest aur Cypress ke latest comparison ke liye [Vitest's comparison page](https://vitest.dev/guide/comparisons.html#cypress) dekho.

---

### Mounting Libraries {#mounting-libraries}

Component testing me aksar component ko isolation me mount karna, simulated user input events trigger karna, aur rendered DOM output pe assertions karna shamil hota hai. Ye tasks simplify karne ke liye dedicated utility libraries hain:

- [`@vue/test-utils`](https://github.com/vuejs/test-utils) official low-level component testing library hai jo Vue specific APIs access karne ke liye likhi gayi hai. Yehi wo lower-level library hai jiske upar `@testing-library/vue` build kiya gaya hai.

- [`@testing-library/vue`](https://github.com/testing-library/vue-testing-library) ek Vue testing library hai jo components ko bina implementation details pe rely kiye test karne pe focus karti hai. Iska guiding principle hai: jitna zyada tests software ke actual usage jaise lagte hain, utna hi zyada confidence dete hain.

Applications me components test karne ke liye hum recommend karte hain ki `@vue/test-utils` use karo. `@testing-library/vue` ka Suspense ke sath asynchronous component testing me issues hain, isliye ise caution ke sath use karna chahiye.

---

### Other Options {#other-options-1}

- [Nightwatch](https://nightwatchjs.org/) ek E2E test runner hai jisme Vue Component Testing support hai. ([Example Project](https://github.com/nightwatchjs-community/todo-vue))

- [WebdriverIO](https://webdriver.io/docs/component-testing/vue) cross-browser component testing ke liye jo standardized automation par based native user interaction use karta hai. Ye Testing Library ke sath bhi use kiya ja sakta hai.

---

## E2E Testing {#e2e-testing}

Unit tests developers ko thoda confidence dete hain, lekin unit aur component tests limited hote hain holistic coverage dene me jab application production me hota hai. Isi wajah se end-to-end (E2E) tests sabse important aspect ko cover karte hain: jab users actually application use karte hain toh kya hota hai.

End-to-end tests multi-page application behavior pe focus karte hain jo production-built Vue application ke against network requests banate hain. Ye aksar ek database ya backend setup involve karte hain, aur kabhi kabhi live staging environment ke against bhi run hote hain.

E2E tests aksar router, state management library, top-level components (jaise App ya Layout), public assets, ya request handling ke issues catch karte hain. Ye wo critical issues pakad lete hain jo unit ya component tests se catch karna mushkil hota hai.

E2E tests aapki Vue application ke kisi bhi code ko import nahi karte, balki poore pages ko real browser me navigate karke test karte hain.

E2E tests application ke kai layers ko validate karte hain. Ye aapke locally built app pe ya live staging environment pe target kar sakte hain. Staging environment pe test karna frontend code, static server, aur associated backend services sabko include karta hai.

> Jitna zyada aapke tests software ke actual usage ko resemble karte hain, utna hi zyada confidence dete hain. - [Kent C. Dodds](https://twitter.com/kentcdodds/status/977018512689455106) - Testing Library ke author

User actions application pe kaise impact karte hain ye test karke, E2E tests higher confidence dete hain ki app sahi kaam kar raha hai ya nahi.

---

### Choosing an E2E Testing Solution {#choosing-an-e2e-testing-solution}

Web pe end-to-end (E2E) testing ka reputation hai unreliable (flaky) tests aur development ko slow karne ka. Lekin modern E2E tools ne reliable, interactive, aur useful tests banane me progress kiya hai. Testing framework choose karte waqt ye points dhyan me rakho:

#### Cross-browser testing {#cross-browser-testing}

E2E testing ka ek major benefit hai ki ye app ko multiple browsers me test kar sakta hai. Lekin 100% cross-browser coverage hamesha efficient nahi hoti, kyunki consistently run karne me zyada time aur resources lagte hain. Isliye trade-off samajhna important hai jab decide kar rahe ho ki kitna cross-browser testing chahiye.

#### Faster feedback loops {#faster-feedback-loops}

E2E tests ka ek common problem hai ki pura suite run karna bahut time leta hai. Ye typically CI/CD pipelines me run hota hai. Modern frameworks me parallelization jaise features hote hain jo tests ko magnitudes faster bana dete hain. Local development ke time pe single test run karna aur hot reloading available hona developer productivity ko boost karta hai.

#### First-class debugging experience {#first-class-debugging-experience}

Pehle developers terminal logs pe depend karte the samajhne ke liye ki test me kya galat hua. Modern E2E frameworks allow karte hain ki aap familiar tools (jaise browser devtools) ka use karo debugging ke liye.

#### Visibility in headless mode {#visibility-in-headless-mode}

CI/CD pipelines me E2E tests aksar headless browsers me run hote hain. Modern frameworks me snapshots aur videos available hote hain jo errors samajhne me madad karte hain.

---

### Recommendation {#recommendation-2}

- [Playwright](https://playwright.dev/) ek great E2E testing solution hai jo Chromium, WebKit, aur Firefox support karta hai. Ye Windows, Linux, macOS par run hota hai, locally ya CI par, headless ya headed. Isme informative UI, excellent debuggability, built-in assertions, parallelization, traces hote hain aur flaky tests eliminate karne ke liye design hua hai. Component Testing support hai (experimental). Playwright open source hai aur Microsoft maintain karta hai.

- [Cypress](https://www.cypress.io/) me informative graphical interface, excellent debuggability, built-in assertions, stubs, flake-resistance, aur snapshots hote hain. Ye stable component testing support deta hai ([docs](https://docs.cypress.io/guides/component-testing/introduction)). Cypress Chromium-based browsers, Firefox, aur Electron support karta hai. WebKit support experimental hai. Cypress MIT-licensed hai, lekin parallelization jaise kuch features Cypress Cloud subscription require karte hain.

<div class="lambdatest">
  <a href="https://lambdatest.com" target="_blank">
    <img src="/images/lambdatest.svg">
    <div>
      <div class="testing-partner">Testing Sponsor</div>
      <div>
        Lambdatest ek cloud platform hai jo E2E, accessibility, aur visual regression tests ko
        saare major browsers aur real devices par run karne ki facility deta hai, AI-assisted test generation ke sath!
      </div>
    </div>
  </a>
</div>

### Other Options {#other-options-2}

- [Nightwatch](https://nightwatchjs.org/) ek E2E testing solution hai jo [Selenium WebDriver](https://www.npmjs.com/package/selenium-webdriver) par based hai. Is wajah se ye sabse wide browser support deta hai, including native mobile testing. Selenium-based solutions Playwright ya Cypress se slow hoti hain.

- [WebdriverIO](https://webdriver.io/) ek test automation framework hai web aur mobile testing ke liye jo WebDriver protocol par based hai.

## Recipes {#recipes}

### Project me Vitest add karna {#adding-vitest-to-a-project}

Ek Vite-based Vue project me, run karein:

```sh
> npm install -D vitest happy-dom @testing-library/vue
```

Next, Vite configuration update karein aur `test` option block add karein:

```js{5-11} [vite.config.js]
import { defineConfig } from 'vite'

export default defineConfig({
  // ...
  test: {
    // enable jest-like global test APIs
    globals: true,
    // simulate DOM with happy-dom
    // (requires installing happy-dom as a peer dependency)
    environment: 'happy-dom'
  }
})
```

:::tip
Agar aap TypeScript use karte ho, toh `tsconfig.json` ke `types` field mein `vitest/globals` add karo.

```json [tsconfig.json]
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

:::

Phir, apne project mein `*.test.js` se end hone wali ek file banao.Aap chaaho toh saari test files ko project root ke andar ek test directory mein rakh sakte ho, ya phir source files ke paas hi test directories bana sakte ho. Vitest inhe automatically dhund lega naming convention ke basis par.

```js [MyComponent.test.js]
import { render } from '@testing-library/vue'
import MyComponent from './MyComponent.vue'

test('it should work', () => {
  const { getByText } = render(MyComponent, {
    props: {
      /* ... */
    }
  })

  // assert output
  getByText('...')
})
```

Aakhri step mein, `package.json` ko update karo aur test script add karke usse run karo:

```json{4} [package.json]
{
  // ...
  "scripts": {
    "test": "vitest"
  }
}
```

```sh
> npm test
```

### Testing Composables {#testing-composables}

> Ye section assume karta hai ki aapne [Composables](/guide/reusability/composables) section padha hua hai.

Composables test karte time, hum unhe do categories mein divide kar sakte hain:

1. Composables jo host component instance par depend **nahi** karte.
2. Composables jo host component instance par depend karte hain.

Ek composable host component instance par tab depend karta hai jab wo in APIs ka use karta hai:

- Lifecycle hooks
- Provide / Inject

Agar ek composable sirf Reactivity APIs ka use karta hai, toh usse directly invoke karke aur uske return kiye gaye state/methods ko assert karke test kiya ja sakta hai:

```js [counter.js]
import { ref } from 'vue'

export function useCounter() {
  const count = ref(0)
  const increment = () => count.value++

  return {
    count,
    increment
  }
}
```

```js [counter.test.js]
import { useCounter } from './counter.js'

test('useCounter', () => {
  const { count, increment } = useCounter()
  expect(count.value).toBe(0)

  increment()
  expect(count.value).toBe(1)
})
```

Ek composable jo lifecycle hooks ya Provide / Inject par rely karta hai, use test karne ke liye host component ke andar wrap karna padta hai. Hum ek helper function aise create kar sakte hain:

```js [test-utils.js]
import { createApp } from 'vue'

export function withSetup(composable) {
  let result
  const app = createApp({
    setup() {
      result = composable()
      // suppress missing template warning
      return () => {}
    }
  })
  app.mount(document.createElement('div'))
  // return the result and the app instance
  // for testing provide/unmount
  return [result, app]
}
```

```js

test('useFoo', () => {
  const [result, app] = withSetup(() => useFoo(123))
  // mock provide for testing injections
  app.provide(...)
  // run assertions
  expect(result.foo.value).toBe(1)
  // trigger onUnmounted hook if needed
  app.unmount()
})
```

Zyada complex composables ke liye, unhe test karna easy ho sakta hai agar hum wrapper component ke against tests likhein using [Component Testing](#component-testing) techniques.

<!--
TODO more testing recipes can be added in the future e.g.
- How to set up CI via GitHub actions
- How to do mocking in component testing
-->
