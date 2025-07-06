# Template Syntax {#template-syntax}

<ScrimbaLink href="https://scrimba.com/links/vue-template-syntax" title="Free Vue.js Template Syntax Lesson" type="scrimba">
  Scrimba par ek interactive video lesson dekhein
</ScrimbaLink>

Vue ek HTML-based template syntax ka use karta hai jo aapko rendered DOM ko component instance ke data ke saath declaratively bind karne deta hai. Vue ke sabhi templates syntactically valid HTML hote hain jise spec-compliant browsers aur HTML parsers easily parse kar sakte hain.

Backend mein, Vue in templates ko highly-optimized JavaScript code mein compile karta hai. Reactivity system ke saath milke, Vue smartly identify karta hai ki sirf kaunse components re-render hone chahiye aur DOM mein sirf minimal changes apply karta hai jab app ka state change hota hai.

Agar aap Virtual DOM concepts se familiar ho aur JavaScript ka raw control pasand karte ho, to aap [directly render functions likh sakte ho](/guide/extras/render-function), jisme optional JSX support bhi hai. Lekin dhyan rahe ki inme templates jaise compile-time optimizations nahi milte.

## Text Interpolation {#text-interpolation}

Data binding ka sabse basic form hai text interpolation, jisme "Mustache" syntax (double curly braces) use kiya jata hai:

```vue-html
<span>Message: {{ msg }}</span>
```

Mustache tag ko component instance ki [`msg` property](/guide/essentials/reactivity-fundamentals#declaring-reactive-state) ke value se replace kiya jaayega. Ye value tab bhi update hoti rahegi jab `msg` property change hoti hai.

## Raw HTML {#raw-html}

Double mustaches data ko plain text ke roop mein interpret karte hain, HTML ke roop mein nahi. Agar aap actual HTML output karna chahte ho, to aapko [`v-html` directive](/api/built-in-directives#v-html) ka use karna hoga:

```vue-html
<p>Using text interpolation: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

<script setup>
  const rawHtml = '<span style="color: red">This should be red.</span>'
</script>

<div class="demo">
  <p>Text interpolation ka use: {{ rawHtml }}</p>
  <p>v-html directive ka use: <span v-html="rawHtml"></span></p>
</div>

Yahan hum ek naya concept dekh rahe hain. Jo `v-html` attribute aap dekh rahe ho, usse **directive** kehte hain. Directives `v-` prefix ke saath aate hain taaki yeh bataya ja sake ki yeh Vue ke special attributes hain. Jaise ki aapne guess bhi kiya hoga, yeh directives rendered DOM par special reactive behavior apply karte hain. Is case mein hum basically keh rahe hain: "is element ke andar ka HTML hamesha `rawHtml` property ke value ke saath sync rehna chahiye."

`span` ke andar ka content `rawHtml` property ke value se replace ho jaayega, jo plain HTML ke roop mein interpret hoga — data bindings ismein kaam nahi karte. Dhyan rahe ki aap `v-html` ka use template partials compose karne ke liye nahi kar sakte, kyunki Vue ek string-based templating engine nahi hai. Iske bajaye, components ko hi UI reuse aur composition ke base unit ke roop mein use karna recommended hai.

:::warning Security Warning
Aapke website par dynamically koi bhi HTML render karna dangerous ho sakta hai, kyunki isse [XSS vulnerabilities](https://en.wikipedia.org/wiki/Cross-site_scripting) aasani se ho sakti hain. `v-html` sirf trusted content ke liye use karein aur **kabhi bhi** user-provided content pe use na karein.
:::

## Attribute Bindings {#attribute-bindings}

Mustache syntax ko HTML attributes ke andar use nahi kiya ja sakta. Iske bajaye aap [`v-bind` directive](/api/built-in-directives#v-bind) ka use karein:

```vue-html
<div v-bind:id="dynamicId"></div>
```

`v-bind` directive Vue ko yeh batata hai ki element ke `id` attribute ko component ki `dynamicId` property ke saath sync mein rakha jaye. Agar bound value `null` ya `undefined` ho, to yeh attribute rendered element se hata diya jaayega.

### Shorthand {#shorthand}

Kyuki `v-bind` kaafi commonly use hota hai, iske liye ek dedicated shorthand syntax bhi diya gaya hai:

```vue-html
<div :id="dynamicId"></div>
```

Jo attributes `:` se start hote hain wo normal HTML se thoda alag lag sakte hain,  
lekin yeh actual mein valid attribute names hote hain aur saare Vue-supported browsers inhe sahi se parse kar lete hain.  
Yeh attributes final rendered markup mein dikhte bhi nahi.  
Shorthand syntax optional hai, lekin jab aap aage iska use seekhenge to yeh kaafi convenient lagega.

> Guide ke aage ke parts mein, hum code examples mein shorthand syntax ka use karenge,  
> kyunki yeh Vue developers ke beech sabse common usage hai.

### Same-name Shorthand {#same-name-shorthand}

- Sirf Vue 3.4+ mein supported

Agar attribute ka naam us JavaScript variable ke naam ke barabar ho jo bind ho raha hai,  
toh syntax ko aur short kiya ja sakta hai — attribute value ko completely chhod kar:

```vue-html
<!-- same as :id="id" -->
<div :id></div>

<!-- this also works -->
<div v-bind:id></div>
```

Yeh JavaScript mein object declare karte waqt use hone wale property shorthand syntax jaisa hi hai.  
Dhyan rahe ki yeh feature sirf Vue 3.4 aur uske upar ke versions mein hi available hai.

### Boolean Attributes {#boolean-attributes}

[Boolean attributes](https://html.spec.whatwg.org/multipage/common-microsyntaxes.html#boolean-attributes) wo attributes hote hain jo kisi element par apni presence ke zariye true / false value indicate karte hain.  
Jaise ki [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/disabled) ek commonly used boolean attribute hai.

`v-bind` ka behaviour is case mein thoda alag hota hai:

```vue-html
<button :disabled="isButtonDisabled">Button</button>
```

Agar `isButtonDisabled` ka value [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) hai, toh `disabled` attribute include kiya jaayega.  
Yeh attribute tab bhi include hoga agar value ek empty string ho — jo `<button disabled="">` ke behavior ke saath consistent hai. Baaki [falsy values](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) ke liye yeh attribute rendered element mein nahi hoga.

### Dynamically Multiple Attributes Bind Karna {#dynamically-binding-multiple-attributes}

Agar aapke paas ek JavaScript object hai jo multiple attributes ko represent karta hai, aur wo kuch is tarah dikhta hai:

<div class="composition-api">

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    objectOfAttrs: {
      id: 'container',
      class: 'wrapper'
    }
  }
}
```

</div>

Aap in attributes ko ek hi element par bind kar sakte ho `v-bind` ko bina kisi argument ke use karke:

```vue-html
<div v-bind="objectOfAttrs"></div>
```

## JavaScript Expressions ka Use Karna {#using-javascript-expressions}

Ab tak humne templates mein sirf simple property keys ko bind kiya hai. Lekin Vue sabhi data bindings ke andar JavaScript expressions ka full power support karta hai:

```vue-html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

Yeh expressions current component instance ke data scope ke andar JavaScript ke roop mein evaluate kiye jaate hain.

Vue templates mein JavaScript expressions in positions mein use kiye ja sakte hain:

- Text interpolations (mustaches) ke andar
- Kisi bhi Vue directive ke attribute value mein (wo attributes jo `v-` se start hote hain)

### Sirf Expressions Allowed Hain {#expressions-only}

Har binding mein sirf **ek single expression** allowed hoti hai.  
Expression ek aisa code hota hai jo kisi value mein evaluate ho sakta hai.  
Ek simple check yeh hai: kya yeh code `return` ke baad likhne layak hai?

Is wajah se, neeche diye gaye examples **kaam nahi karenge**:

```vue-html
<!-- yeh ek statement hai, expression nahi: -->
{{ var a = 1 }}

<!-- flow control bhi kaam nahi karega, ternary expressions ka use karo -->
{{ if (ok) { return message } }}
```

### Functions ko Call Karna {#calling-functions}

Aap kisi binding expression ke andar component ka expose kiya gaya method call kar sakte ho:

```vue-html
<time :title="toTitleDate(date)" :datetime="date">
  {{ formatDate(date) }}
</time>
```

:::tip
Binding expressions ke andar call hone wale functions har baar component ke update hone par dobara call hote hain,  
isliye unme **koi side effects nahi hone chahiye**, jaise ki data ko change karna ya asynchronous operations trigger karna.
:::

### Restricted Globals Access {#restricted-globals-access}

Template expressions sandboxed hoti hain aur sirf ek [restricted globals list](https://github.com/vuejs/core/blob/main/packages/shared/src/globalsAllowList.ts#L3) tak hi access hota hai.  
Is list mein commonly used built-in globals jaise `Math` aur `Date` included hote hain.

Jo globals explicitly is list mein included nahi hote —  
jaise `window` object par user-attached properties —  
unhe template expressions ke andar access nahi kiya ja sakta.  
Lekin aap chaho toh Vue expressions ke liye additional globals explicitly define kar sakte ho  
by adding them to [`app.config.globalProperties`](/api/application#app-config-globalproperties).

## Directives {#directives}

Directives aise special attributes hote hain jo `v-` prefix ke saath hote hain.  
Vue kuch [built-in directives](/api/built-in-directives) provide karta hai,  
jaise `v-html` aur `v-bind` jinke baare mein hum upar dekh chuke hain.

Directive attribute values ek single JavaScript expression hoti hain  
(except `v-for`, `v-on` aur `v-slot`, jinhe hum unke respective sections mein detail mein discuss karenge).  
Directive ka kaam yeh hota hai ki jab uske expression ka value change ho,  
toh wo reactively DOM par update apply kare.  
Jaise [`v-if`](/api/built-in-directives#v-if) ko hi le lo:

```vue-html
<p v-if="seen">Now you see me</p>
```

Yahan `v-if` directive `<p>` element ko tab insert ya remove karega jab `seen` expression ka value truthy ya falsy hoga.

### Arguments {#arguments}

Kuch directives ek "argument" bhi le sakte hain, jo directive ke naam ke baad colon (`:`) lagakar diya jaata hai.  
Jaise `v-bind` directive HTML attribute ko reactively update karne ke liye use hota hai:

```vue-html
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```

Yahan `href` ek argument hai, jo `v-bind` directive ko yeh batata hai ki element ke `href` attribute ko `url` expression ke value se bind kiya jaye.  
Shorthand mein, argument se pehle jo part hota hai (jaise `v-bind:`), usko ek single character `:` mein convert kar diya jaata hai.

Ek aur example hai `v-on` directive, jo DOM events ko listen karta hai:

```vue-html
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

Yahan argument hota hai event ka naam jise listen karna hai: `click`.  
`v-on` ka bhi ek shorthand hota hai, jo hota hai `@` character.  
Event handling ke baare mein hum aage detail mein baat karenge.

### Dynamic Arguments {#dynamic-arguments}

Directive argument ke roop mein JavaScript expression bhi use kiya ja sakta hai —  
iske liye us expression ko square brackets `[]` ke andar likhna hota hai:

```vue-html
<!--
Dhyan rahe ki argument expression ke upar kuch constraints hote hain,
jinke baare mein neeche "Dynamic Argument Value Constraints"
aur "Dynamic Argument Syntax Constraints" sections mein explain kiya gaya hai.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- shorthand -->
<a :[attributeName]="url"> ... </a>
```

Yahan `attributeName` ko ek JavaScript expression ke roop mein dynamically evaluate kiya jaayega,  
aur uska evaluated value final argument ke roop mein use hoga.  
Maan lijiye aapke component instance mein ek data property hai `attributeName`, jiska value `"href"` hai,  
toh yeh binding `v-bind:href` ke barabar hogi.

Isi tarah, aap dynamic arguments ka use karke kisi dynamic event name ke saath event handler bhi bind kar sakte ho:

```vue-html
<a v-on:[eventName]="doSomething"> ... </a>

<!-- shorthand -->
<a @[eventName]="doSomething"> ... </a>
```

Is example mein, jab `eventName` ka value `"focus"` hota hai,  
toh `v-on:[eventName]` ka matlab hota hai `v-on:focus`.

#### Dynamic Argument Value Constraints {#dynamic-argument-value-constraints}

Dynamic arguments ka evaluate hona expected hota hai ek string mein,  
`null` ko chhodkar. `null` ek special value hai jiska use binding ko explicitly remove karne ke liye kiya ja sakta hai.  
Agar koi aur non-string value di jaati hai, toh ek warning trigger hoti hai.

#### Dynamic Argument Syntax Constraints {#dynamic-argument-syntax-constraints}

Dynamic argument expressions ke kuch syntax constraints hote hain,  
kyunki kuch characters jaise space aur quotes HTML attribute names ke andar valid nahi hote.  
Maan lijiye, yeh example invalid hai:

```vue-html
<!-- This will trigger a compiler warning. -->
<a :['foo' + bar]="value"> ... </a>
```

Agar aapko koi complex dynamic argument pass karna hai,  
toh behtar hoga ki aap ek [computed property](./computed) ka use karo — jiske baare mein hum thodi der mein discuss karenge.

Jab aap in-DOM templates ka use kar rahe ho (yaani templates jo directly HTML file mein likhe gaye ho),  
toh aapko attribute keys mein uppercase characters ka use avoid karna chahiye,  
kyunki browsers attribute names ko automatically lowercase mein convert kar dete hain:

```vue-html
<a :[someAttr]="value"> ... </a>
```

Upar wala example in-DOM templates mein `:[someattr]` mein convert ho jaayega.  
Agar aapke component mein `someAttr` property hai (na ki `someattr`), to aapka code kaam nahi karega.  
Lekin Single-File Components ke andar likhe gaye templates is constraint se affected nahi hote.

### Modifiers {#modifiers}

Modifiers ek special postfix hote hain jo dot (`.`) ke saath likhe jaate hain,  
aur yeh batate hain ki kisi directive ko ek specific tareeke se bind karna hai.  
Jaise `.prevent` modifier `v-on` directive ko yeh instruct karta hai ki event trigger hone par `event.preventDefault()` call kare:

```vue-html
<form @submit.prevent="onSubmit">...</form>
```

Aap modifiers ke aur bhi examples aage dekhenge — [jaise `v-on`](./event-handling#event-modifiers) ke liye aur [`v-model`](./forms#modifiers) ke liye — jab hum un features ko detail mein explore karenge.

Aur finally, yeh rahi full directive syntax ki ek visual representation:

![directive syntax graph](./images/directive.png)

<!-- https://www.figma.com/file/BGWUknIrtY9HOmbmad0vFr/Directive -->
