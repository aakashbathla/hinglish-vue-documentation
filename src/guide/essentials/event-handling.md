# Event Handling {#event-handling}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/user-events-in-vue-3" title="Free Vue.js Events Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-events-in-vue-3" title="Free Vue.js Events Lesson"/>
</div>

## Listening to Events {#listening-to-events}

Hum `v-on` directive ka use karte hain, jise aam taur par `@` symbol se shorten kiya jata hai, DOM events ko sunne ke liye aur jab woh trigger ho tab JavaScript run karne ke liye. Iska use hota hai `v-on:click="handler"` ya shortcut ke roop mein `@click="handler"`.

Handler value inme se koi bhi ho sakti hai:

1. **Inline handlers:** Inline JavaScript jo event trigger hone par execute hoti hai (jaise native `onclick` attribute).
2. **Method handlers:** Ek property name ya path jo component mein define ki gayi method ko point karta hai.

## Inline Handlers {#inline-handlers}

Inline handlers aam taur par simple cases mein use ki jaati hain, jaise:

<div class="composition-api">

```js
const count = ref(0)
```

</div>
<div class="options-api">

```js
data() {
  return {
    count: 0
  }
}
```

</div>

```vue-html
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNo9jssKgzAURH/lko0tgrbbEqX+Q5fZaLxiqHmQ3LgJ+fdqFZcD58xMYp1z1RqRvRgP0itHEJCia4VR2llPkMDjBBkmbzUUG1oII4y0JhBIGw2hh2Znbo+7MLw+WjZ/C4TaLT3hnogPkcgaeMtFyW8j2GmXpWBtN47w5PWBHLhrPzPCKfWDXRHmPsCAaOBfgSOkdH3IGUhpDBWv9/e8vsZZ/gFFhFJN)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNo9jcEKgzAQRH9lyKlF0PYqqdR/6DGXaLYo1RjiRgrivzepIizLzu7sm1XUzuVLIFEKObe+d1wpS183eYahtw4DY1UWMJr15ZpmxYAnDt7uF0BxOwXL5Evc0kbxlmyxxZLFyY2CaXSDZkqKZROYJ4tnO/Tt56HEgckyJaraGNxlsVt2u6teHeF40s20EDo9oyGy+CPIYF1xULBt4H6kOZeFiwBZnOFi+wH0B1hk)

</div>

## Method Handlers {#method-handlers}

Kaafi events ka logic complex hota hai, jise inline likhna feasible nahi hota. Isi wajah se `v-on` component method ka naam ya path bhi accept karta hai, jise aap call karna chahte ho.

For example:

<div class="composition-api">

```js
const name = ref('Vue.js')

function greet(event) {
  alert(`Hello ${name.value}!`)
  // `event` is the native DOM event
  if (event) {
    alert(event.target.tagName)
  }
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    name: 'Vue.js'
  }
},
methods: {
  greet(event) {
    // `this` inside methods points to the current active instance
    alert(`Hello ${this.name}!`)
    // `event` is the native DOM event
    if (event) {
      alert(event.target.tagName)
    }
  }
}
```

</div>

```vue-html
<!-- `greet` is the name of the method defined above -->
<button @click="greet">Greet</button>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNpVj0FLxDAQhf/KMwjtXtq7dBcFQS/qzVMOrWFao2kSkkkvpf/dJIuCEBgm771vZnbx4H23JRJ3YogqaM+IxMlfpNWrd4GxI9CMA3NwK5psbaSVVjkbGXZaCediaJv3RN1XbE5FnZNVrJ3FEoi4pY0sn7BLC0yGArfjMxnjcLsXQrdNJtFxM+Ys0PcYa2CEjuBPylNYb4THtxdUobj0jH/YX3D963gKC5WyvGZ+xR7S5jf01yPzeblhWr2ZmErHw0dizivfK6PV91mKursUl6dSh/4qZ+vQ/+XE8QODonDi)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNplUE1LxDAQ/StjEbYL0t5LXRQEvag3Tz00prNtNE1CMilC6X83SUkRhJDJfLz3Jm8tHo2pFo9FU7SOW2Ho0in8MdoSDHhlXhKsnQIYGLHyvL8BLJK3KmcAis3YwOnDY/XlTnt1i2G7i/eMNOnBNRkwWkQqcUFFByVAXUNPk3A9COXEgBkGRgtFDkgDTQjcWxuAwDiJBeMsMcUxszCJlsr+BaXUcLtGwiqut930579KST1IBd5Aqlgie3p/hdTIk+IK//bMGqleEbMjxjC+BZVDIv0+m9CpcNr6MDgkhLORjDBm1H56Iq3ggUvBv++7IhnUFZfnGNt6b4fRtj5wxfYL9p+Sjw==)

</div>

A method handler ko automatically woh native DOM Event object milta hai jo usse trigger karta hai — upar wale example mein, hum us element ko access kar pa rahe hain jo event dispatch kar raha hai via `event.target`.

<div class="composition-api">

See also: [Typing Event Handlers](/guide/typescript/composition-api#typing-event-handlers) <sup class="vt-badge ts" />

</div>
<div class="options-api">

See also: [Typing Event Handlers](/guide/typescript/options-api#typing-event-handlers) <sup class="vt-badge ts" />

</div>

### Method vs. Inline Detection {#method-vs-inline-detection}

Template compiler method handlers ko detect karta hai by checking agar `v-on` ka value string ek valid JavaScript identifier ya property access path hai. Jaise ki `foo`, `foo.bar` aur `foo['bar']` method handlers ke roop mein treat kiye jaate hain, jabki `foo()` aur `count++` inline handlers maane jaate hain.

## Calling Methods in Inline Handlers {#calling-methods-in-inline-handlers}

Ek method name ko directly bind karne ke bajaye, hum inline handler ke andar method ko call bhi kar sakte hain. Isse hume method ko native event ke bajaye custom arguments pass karne ki flexibility milti hai:

<div class="composition-api">

```js
function say(message) {
  alert(message)
}
```

</div>
<div class="options-api">

```js
methods: {
  say(message) {
    alert(message)
  }
}
```

</div>

```vue-html
<button @click="say('hello')">Say hello</button>
<button @click="say('bye')">Say bye</button>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNp9jTEOwjAMRa8SeSld6I5CBWdg9ZJGBiJSN2ocpKjq3UmpFDGx+Vn//b/ANYTjOxGcQEc7uyAqkqTQI98TW3ETq2jyYaQYzYNatSArZTzNUn/IK7Ludr2IBYTG4I3QRqKHJFJ6LtY7+zojbIXNk7yfmhahv5msvqS7PfnHGjJVp9w/hu7qKKwfEd1NSg==)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNptjUEKwjAQRa8yZFO7sfsSi57B7WzGdjTBtA3NVC2ldzehEFwIw8D7vM9f1cX742tmVSsd2sl6aXDgjx8ngY7vNDuBFQeAnsWMXagToQAEWg49h0APLncDAIUcT5LzlKJsqRBfPF3ljQjCvXcknEj0bRYZBzi3zrbPE6o0UBhblKiaKy1grK52J/oA//23IcmNBD8dXeVBtX0BF0pXsg==)

</div>

## Accessing Event Argument in Inline Handlers {#accessing-event-argument-in-inline-handlers}

Kabhi-kabhi hume inline handler ke andar original DOM event ko bhi access karne ki zarurat hoti hai. Aap isse method mein special `$event` variable ke through pass kar sakte ho, ya ek inline arrow function ka use kar sakte ho:

```vue-html
<!-- using $event special variable -->
<button @click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

<!-- using inline arrow function -->
<button @click="(event) => warn('Form cannot be submitted yet.', event)">
  Submit
</button>
```

<div class="composition-api">

```js
function warn(message, event) {
  // now we have access to the native event
  if (event) {
    event.preventDefault()
  }
  alert(message)
}
```

</div>
<div class="options-api">

```js
methods: {
  warn(message, event) {
    // now we have access to the native event
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

</div>

## Event Modifiers {#event-modifiers}

Yeh kaafi common requirement hoti hai ki hum `event.preventDefault()` ya `event.stopPropagation()` ko event handlers ke andar call karein. Halanki yeh methods ke andar easily kiya ja sakta hai, lekin yeh behtar hota agar methods sirf data logic tak simit rahte, bina DOM event details ko handle kiye.

Is problem ko solve karne ke liye, Vue `v-on` ke liye **event modifiers** provide karta hai. Yaad rahe ki modifiers directive ke postfix hote hain jo dot (`.`) se denote kiye jaate hain.

- `.stop`
- `.prevent`
- `.self`
- `.capture`
- `.once`
- `.passive`

```vue-html
<!-- the click event's propagation will be stopped -->
<a @click.stop="doThis"></a>

<!-- the submit event will no longer reload the page -->
<form @submit.prevent="onSubmit"></form>

<!-- modifiers can be chained -->
<a @click.stop.prevent="doThat"></a>

<!-- just the modifier -->
<form @submit.prevent></form>

<!-- only trigger handler if event.target is the element itself -->
<!-- i.e. not from a child element -->
<div @click.self="doThat">...</div>
```

::: tip
Modifiers ka order matter karta hai kyunki relevant code bhi usi order mein generate hota hai. Isliye agar aap `@click.prevent.self` likhte ho, toh yeh **element aur uske children** par click ka default action prevent karega. Wahi agar aap `@click.self.prevent` likhte ho, toh yeh sirf element ke upar click ka default action prevent karega.
:::

`.capture`, `.once`, aur `.passive` modifiers native [`addEventListener` method](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#options) ke options ko mirror karte hain:

````vue-html
<!-- capture mode ka use jab aap event listener add karte ho -->
<!-- yani ek event jo kisi inner element ko target karta hai  -->
<!-- usse pehle yahan handle kiya jaayega                    -->
<div @click.capture="doThis">...</div>

<!-- click event sirf ek baar trigger hoga -->
<a @click.once="doThis"></a>

<!-- scroll event ka default behavior (scroll karna) turant hoga -->
<!-- bina `onScroll` ke complete hone ka wait kiye               -->
<!-- agar usme `event.preventDefault()` likha ho to bhi          -->
<div @scroll.passive="onScroll">...</div>


`.passive` modifier ko aam taur par touch event listeners ke saath use kiya jata hai [mobile devices par performance improve karne ke liye](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#improving_scroll_performance_using_passive_listeners).

::: tip
`.passive` aur `.prevent` ko ek saath use na karein, kyunki `.passive` browser ko pehle se hi yeh batata hai ki aap event ka default behavior prevent **nahi** karna chahte. Agar aap aisa karte hain to browser warning bhi de sakta hai.
:::


## Key Modifiers {#key-modifiers}

Jab hum keyboard events ke liye listen karte hain, to aksar hume specific keys ko check karna padta hai. Vue allow karta hai `v-on` ya `@` ke saath key modifiers add karna jab aap key events sun rahe ho:

```vue-html
<!-- sirf tab `submit` call hoga jab `key` ho `Enter` -->
<input @keyup.enter="submit" />


Aap kisi bhi valid key name ko — jo [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/UI_Events/Keyboard_event_key_values) ke through milte hain — modifiers ke roop mein directly use kar sakte ho, bas unhe kebab-case mein convert karna hota hai.

```vue-html
<input @keyup.page-down="onPageDown" />
```
Upar wale example mein, handler sirf tabhi call hoga jab `$event.key` ki value `'PageDown'` ke barabar ho.

### Key Aliases {#key-aliases}

Vue kuch commonly used keys ke liye predefined aliases provide karta hai:

- `.enter`
- `.tab`
- `.delete` (yeh "Delete" aur "Backspace" dono keys ko capture karta hai)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

### System Modifier Keys {#system-modifier-keys}

Aap neeche diye gaye modifiers ka use kar sakte ho taaki mouse ya keyboard event listeners sirf tab trigger ho jab corresponding modifier key press ho:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

::: tip Note
Macintosh keyboards par meta ka matlab hota hai command key (⌘). Windows keyboards par yeh Windows key (⊞) hoti hai. Sun Microsystems keyboards par meta ek solid diamond (◆) se mark kiya jata hai. Kuch specific keyboards, jaise MIT aur Lisp machine keyboards (jaise Knight ya space-cadet keyboard) mein meta ko “META” label diya jata hai. Symbolics keyboards par bhi yeh “META” ya “Meta” ke roop mein dikhai deta hai.
:::

For example:

```vue-html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

::: tip
Yaad rahe ki modifier keys regular keys se alag hoti hain, aur jab aap unhe `keyup` events ke saath use karte ho, toh unhe press kiya hua hona chahiye jab event emit ho. Dusre shabdon mein, `keyup.ctrl` sirf tab trigger karega jab aap kisi aur key ko release karo jab `ctrl` press ho. Agar aap sirf `ctrl` key ko release karte ho, toh yeh trigger nahi karega.
:::

### `.exact` Modifier {#exact-modifier}

`.exact` modifier aapko yeh control dene ke liye hota hai ki kaunse exact system modifier keys hone chahiye event ko trigger karne ke liye.


```vue-html
<!-- ye tab bhi chalega jab Alt ya Shift bhi press ho -->
<button @click.ctrl="onClick">A</button>

<!-- ye tabhi chalega jab sirf Ctrl press ho, aur koi aur key nahi -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- ye tabhi chalega jab koi bhi system modifier press na ho -->
<button @click.exact="onClick">A</button>
```
## Mouse Button Modifiers {#mouse-button-modifiers}

- `.left`
- `.right`
- `.middle`

Ye modifiers event handler ko sirf ek specific mouse button ke trigger hone par hi chalne dete hain.

Lekin dhyan rahe, `.left`, `.right`, aur `.middle` modifiers ke naam typical right-handed mouse layout par based hote hain — asal mein ye "main", "secondary", aur "auxiliary" pointing device event triggers ko represent karte hain, na ki actual physical buttons ko.

Iska matlab:

- Agar aap left-handed mouse layout use kar rahe hain, toh "main" button physically right side ka ho sakta hai, lekin fir bhi `.left` modifier trigger hoga.
- Ek trackpad par:
  - one-finger tap `.left` trigger karta hai
  - two-finger tap `.right` trigger karta hai
  - three-finger tap `.middle` trigger karta hai
- Aur kuch devices ya event sources jo "mouse" events generate karte hain, wo triggers aise bhi ho sakte hain jo "left" ya "right" buttons se directly related na ho.

````
