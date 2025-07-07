# Slots {#slots}

> Is page ka assumption hai ki aap pehle hi [Components Basics](/guide/essentials/component-basics) padh chuke ho. Agar aap components ke liye naye ho, to pehle wo padho.

<VueSchoolLink href="https://vueschool.io/lessons/vue-3-component-slots" title="Free Vue.js Slots Lesson"/>

## Slot Content aur Outlet {#slot-content-and-outlet}

Humne seekha ki components props accept karte hain, jo kisi bhi type ke JavaScript values ho sakte hain. Lekin template content ka kya? Kuch cases mein, hum chahte hain ki ek template fragment child component ko pass karein, aur child component us fragment ko apne template ke andar render kare.

Maan lo humare paas ek `<FancyButton>` component hai jiska use kuch is tarah hota hai:

```vue-html{2}
<FancyButton>
  Click me! <!-- slot content -->
</FancyButton>
```

`<FancyButton>` ka template kuch is tarah dikhta hai:

```vue-html{2}
<button class="fancy-btn">
  <slot></slot> <!-- slot outlet -->
</button>
```

`<slot>` element ek **slot outlet** hai jo batata hai ki parent dwara diya gaya **slot content** kahan render hoga.

![slot diagram](./images/slots.png)

<!-- https://www.figma.com/file/LjKTYVL97Ck6TEmBbstavX/slot -->

And the final rendered DOM:

```html
<button class="fancy-btn">Click me!</button>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNpdUdlqAyEU/ZVbQ0kLMdNsXabTQFvoV8yLcRkkjopLSQj596oTwqRvnuM9y9UT+rR2/hs5qlHjqZM2gOch2m2rZW+NC/BDND1+xRCMBuFMD9N5NeKyeNrqphrUSZdA4L1VJPCEAJrRdCEAvpWke+g5NHcYg1cmADU6cB0A4zzThmYckqimupqiGfpXILe/zdwNhaki3n+0SOR5vAu6ReU++efUajtqYGJQ/FIg5w8Wt9FlOx+OKh/nV1c4ZVNqlHE1TIQQ7xnvCN13zkTNalBSc+Jw5wiTac2H1WLDeDeDyXrJVm9LWG7uE3hev3AhHge1cYwnO200L4QljEnd1bCxB1g82UNhe+I6qQs5kuGcE30NrxeaRudzOWtkemeXuHP5tLIKOv8BN+mw3w==)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNpdUdtOwzAM/RUThAbSurIbl1ImARJf0ZesSapoqROlKdo07d9x0jF1SHmIT+xzcY7sw7nZTy9Zwcqu9tqFTYW6ddYH+OZYHz77ECyC8raFySwfYXFsUiFAhXKfBoRUvDcBjhGtLbGgxNAVcLziOlVIp8wvelQE2TrDg6QKoBx1JwDgy+h6B62E8ibLoDM2kAAGoocsiz1VKMfmCCrzCymbsn/GY95rze1grja8694rpmJ/tg1YsfRO/FE134wc2D4YeTYQ9QeKa+mUrgsHE6+zC+vfjoz1Bdwqpd5iveX1rvG2R1GA0Si5zxrPhaaY98v5WshmCrerhVi+LmCxvqPiafUslXoYpq0XkuiQ1p4Ax4XQ2BSwdnuYP7p9QlvuG40JHI1lUaenv3o5w3Xvu2jOWU179oQNn5aisNMvLBvDOg==)

</div>

Slots ke saath, `<FancyButton>` outer `<button>` (aur uski fancy styling) render karne ke liye responsible hota hai, jabki andar ka content parent component se aata hai.

Slots ko samajhne ka ek aur tareeka hai unko JavaScript functions se compare karna:

```js
// parent component slot content pass kar raha hai
FancyButton('Click me!')

// FancyButton apne template mein slot content ko render karta hai
function FancyButton(slotContent) {
  return `<button class="fancy-btn">
      ${slotContent}
    </button>`
}
```

Slot content sirf text tak hi limited nahi hota. Ye koi bhi valid template content ho sakta hai. Maan lo hum multiple elements ya doosre components bhi pass kar rahe ho:

```vue-html
<FancyButton>
  <span style="color:red">Click me!</span>
  <AwesomeIcon name="plus" />
</FancyButton>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNp1UmtOwkAQvspQYtCEgrx81EqCJibeoX+W7bRZaHc3+1AI4QyewH8ewvN4Aa/gbgtNIfFf5+vMfI/ZXbCQcvBmMYiCWFPFpAGNxsp5wlkphTLwQjjdPlljBIdMiRJ6g2EL88O9pnnxjlqU+EpbzS3s0BwPaypH4gqDpSyIQVcBxK3VFQDwXDC6hhJdlZi4zf3fRKwl4aDNtsDHJKCiECqiW8KTYH5c1gEnwnUdJ9rCh/XeM6Z42AgN+sFZAj6+Ux/LOjFaEK2diMz3h0vjNfj/zokuhPFU3lTdfcpShVOZcJ+DZgHs/HxtCrpZlj34eknoOlfC8jSCgnEkKswVSRlyczkZzVLM+9CdjtPJ/RjGswtX3ExvMcuu6mmhUnTruOBYAZKkKeN5BDO5gdG13FRoSVTOeAW2xkLPY3UEdweYWqW9OCkYN6gctq9uXllx2Z09CJ9dJwzBascI7nBYihWDldUGMqEgdTVIq6TQqCEMfUpNSD+fX7/fH+3b7P8AdGP6wA==)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNptUltu2zAQvMpGQZEWsOzGiftQ1QBpgQK9g35oaikwkUiCj9aGkTPkBPnLIXKeXCBXyJKKBdoIoA/tYGd3doa74tqY+b+ARVXUjltp/FWj5GC09fCHKb79FbzXCoTVA5zNFxkWaWdT8/V/dHrAvzxrzrC3ZoBG4SYRWhQs9B52EeWapihU3lWwyxfPDgbfNYq+ejEppcLjYHrmkSqAOqMmAOB3L/ktDEhV4+v8gMR/l1M7wxQ4v+3xZ1Nw3Wtb8S1TTXG1H3cCJIO69oxc5mLUcrSrXkxSi1lxZGT0//CS9Wg875lzJELE/nLto4bko69dr31cFc8auw+3JHvSEfQ7nwbsHY9HwakQ4kes14zfdlYH1VbQS4XMlp1lraRMPl6cr1rsZnB6uWwvvi9hufpAxZfLryjEp5GtbYs0TlGICTCsbaXqKliZDZx/NpuEDsx2UiUwo5VxT6Dkv73BPFgXxRktlUdL2Jh6OoW8O3pX0buTsoTgaCNQcDjoGwk3wXkQ2tJLGzSYYI126KAso0uTSc8Pjy9P93k2d6+NyRKa)

</div>

Slots ka use karke humara `<FancyButton>` zyada flexible aur reusable ban jaata hai. Ab hum ise alag-alag jagah par alag-alag inner content ke saath use kar sakte hain, lekin sabhi mein same fancy styling milegi.

Vue components ka slot mechanism [native Web Component `<slot>` element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot) se inspire hua hai, lekin isme extra capabilities bhi hain jo hum aage dekhenge.

## Render Scope {#render-scope}

Slot content ko parent component ke data scope tak access hota hai, kyunki ye parent mein hi define hota hai. Jaise ki:

```vue-html
<span>{{ message }}</span>
<FancyButton>{{ message }}</FancyButton>
```

Yahaan dono <span v-pre>`{{ message }}`</span> interpolations same content render karenge.

Slot content **child component** ke data tak access nahi kar sakta. Vue templates mein expressions sirf us scope ko access kar sakti hain jahan wo define hui hain, bilkul JavaScript ke lexical scoping ki tarah. Dusre shabdon mein:

> Parent template ke expressions sirf parent scope ko access karte hain; child template ke expressions sirf child scope ko.

## Fallback Content {#fallback-content}

Kabhi-kabhi aisa hota hai jab kisi slot ke liye fallback (yaani default) content define karna useful hota hai, jo tab render ho jab parent koi content na de. Jaise ki `<SubmitButton>` component mein:

```vue-html
<button type="submit">
  <slot></slot>
</button>
```

Hum chaahte hain ki agar parent slot content na de to `<button>` ke andar "Submit" text render ho. "Submit" ko fallback content banane ke liye, hum usse `<slot>` tags ke beech daal sakte hain:

```vue-html{3}
<button type="submit">
  <slot>
    Submit <!-- fallback content -->
  </slot>
</button>
```

Ab jab hum `<SubmitButton>` parent component mein use karte hain bina koi content diye:

```vue-html
<SubmitButton />
```

To fallback content "Submit" render hoga:

```html
<button type="submit">Submit</button>
```

Lekin agar hum content provide karte hain:

```vue-html
<SubmitButton>Save</SubmitButton>
```

To diya gaya content render hoga:

```html
<button type="submit">Save</button>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNp1kMsKwjAQRX9lzMaNbfcSC/oL3WbT1ikU8yKZFEX8d5MGgi2YVeZxZ86dN7taWy8B2ZlxP7rZEnikYFuhZ2WNI+jCoGa6BSKjYXJGwbFufpNJfhSaN1kflTEgVFb2hDEC4IeqguARpl7KoR8fQPgkqKpc3Wxo1lxRWWeW+Y4wBk9x9V9d2/UL8g1XbOJN4WAntodOnrecQ2agl8WLYH7tFyw5olj10iR3EJ+gPCxDFluj0YS6EAqKR8mi9M3Td1ifLxWShcU=)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNp1UEEOwiAQ/MrKxYu1d4Mm+gWvXChuk0YKpCyNxvh3lxIb28SEA8zuDDPzEucQ9mNCcRAymqELdFKu64MfCK6p6Tu6JCLvoB18D9t9/Qtm4lY5AOXwMVFu2OpkCV4ZNZ51HDqKhwLAQjIjb+X4yHr+mh+EfbCakF8AclNVkCJCq61ttLkD4YOgqsp0YbGesJkVBj92NwSTIrH3v7zTVY8oF8F4SdazD7ET69S5rqXPpnigZ8CjEnHaVyInIp5G63O6XIGiIlZMzrGMd8RVfR0q4lIKKV+L+srW+wNTTZq3)

</div>

## Named Slots {#named-slots}

Kabhi-kabhi aisa hota hai jab hume ek hi component ke andar multiple slot outlets ki zarurat hoti hai. Jaise ki ek `<BaseLayout>` component ke template mein:

```vue-html
<div class="container">
  <header>
    <!-- Hume yahan header ka content chahiye -->
  </header>
  <main>
    <!-- Hume yahan main content chahiye -->
  </main>
  <footer>
    <!-- Hume yahan footer ka content chahiye -->
  </footer>
</div>
```

Aise cases ke liye `<slot>` element mein ek special attribute hota hai `name`, jise use karke alag-alag slots ko unique ID de sakte hain taaki decide kar sake ki kaunsa content kahaan render hoga:

```vue-html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Jo `<slot>` outlet `name` ke bina hota hai, uska naam default hota hai.

Jab hum parent component mein `<BaseLayout>` use karte hain, hume multiple slot content fragments pass karne padte hain, jo alag-alag slot outlets ko target karte hain. Yahan **named slots** ka use hota hai.

Named slot pass karne ke liye hum `<template>` element ka use karte hain `v-slot` directive ke saath, aur `v-slot` ko argument ke roop mein slot ka naam dete hain:

```vue-html
<BaseLayout>
  <template v-slot:header>
    <!-- header slot ke liye content -->
  </template>
</BaseLayout>
```

`v-slot` ka ek shorthand bhi hota hai `#`, to `<template v-slot:header>` ko hum chhota karke `<template #header>` likh sakte hain. Iska matlab hai "is template fragment ko child component ke 'header' slot mein render karo".

![named slots diagram](./images/named-slots.png)

<!-- https://www.figma.com/file/2BhP8gVZevttBu9oUmUUyz/named-slot -->

Yeh raha code jisme hum teeno slots ke liye content pass kar rahe hain `<BaseLayout>` ko shorthand syntax ke saath:

```vue-html
<BaseLayout>
  <template #header>
    <h1>Yahaan ho sakta hai ek page title</h1>
  </template>

  <template #default>
    <p>Main content ke liye ek paragraph.</p>
    <p>Aur ek aur paragraph.</p>
  </template>

  <template #footer>
    <p>Yeh raha kuch contact info</p>
  </template>
</BaseLayout>
```

Jab ek component default slot aur named slots dono accept karta hai, to jitne bhi top-level `<template>` ke bina wale nodes hote hain unhe implicitly default slot ka content maan liya jaata hai. To upar wale example ko aise bhi likh sakte hain:

```vue-html
<BaseLayout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <!-- implicit default slot -->
  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</BaseLayout>
```

Ab `<template>` elements ke andar jo bhi hai, woh corresponding slots ko pass kar diya jaayega. Final rendered HTML kuch is tarah dikhega:

```html
<div class="container">
  <header>
    <h1>Yahaan ho sakta hai ek page title</h1>
  </header>
  <main>
    <p>Main content ke liye ek paragraph.</p>
    <p>Aur ek aur paragraph.</p>
  </main>
  <footer>
    <p>Yeh raha kuch contact info</p>
  </footer>
</div>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNp9UsFuwjAM/RWrHLgMOi5o6jIkdtphn9BLSF0aKU2ixEVjiH+fm8JoQdvRfu/5xS8+ZVvvl4cOsyITUQXtCSJS5zel1a13geBdRvyUR9cR1MG1MF/mt1YvnZdW5IOWVVwQtt5IQq4AxI2cau5ccZg1KCsMlz4jzWrzgQGh1fuGYIcgwcs9AmkyKHKGLyPykcfD1Apr2ZmrHUN+s+U5Qe6D9A3ULgA1bCK1BeUsoaWlyPuVb3xbgbSOaQGcxRH8v3XtHI0X8mmfeYToWkxmUhFoW7s/JvblJLERmj1l0+T7T5tqK30AZWSMb2WW3LTFUGZXp/u8o3EEVrbI9AFjLn8mt38fN9GIPrSp/p4/Yoj7OMZ+A/boN9KInPeZZpAOLNLRDAsPZDgN4p0L/NQFOV/Ayn9x6EZXMFNKvQ4E5YwLBczW6/WlU3NIi6i/sYDn5Qu2qX1OF51MsvMPkrIEHg==)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNp9UkFuwjAQ/MoqHLiUpFxQlaZI9NRDn5CLSTbEkmNb9oKgiL934wRwQK3ky87O7njGPicba9PDHpM8KXzlpKV1qWVnjSP4FB6/xcnsCRpnOpin2R3qh+alBig1HgO9xkbsFcG5RyvDOzRq8vkAQLSury+l5lNkN1EuCDurBCFXAMWdH2pGrn2YtShqdCPOnXa5/kKH0MldS7BFEGDFDoEkKSwybo8rskjjaevo4L7Wrje8x4mdE7aFxjiglkWE1GxQE9tLi8xO+LoGoQ3THLD/qP2/dGMMxYZs8DP34E2HQUxUBFI35o+NfTlJLOomL8n04frXns7W8gCVEt5/lElQkxpdmVyVHvP2yhBo0SHThx5z+TEZvl1uMlP0oU3nH/kRo3iMI9Ybes960UyRsZ9pBuGDeTqpwfBAvn7NrXF81QUZm8PSHjl0JWuYVVX1PhAqo4zLYbZarUak4ZAWXv5gDq/pG3YBHn50EEkuv5irGBk=)

</div>

Dubara, named slots ko samajhne mein madad mil sakti hai agar aap ise JavaScript function ke analogy se dekhein:

```js
// passing multiple slot fragments with different names
BaseLayout({
  header: `...`,
  default: `...`,
  footer: `...`
})

// <BaseLayout> renders them in different places
function BaseLayout(slots) {
  return `<div class="container">
      <header>${slots.header}</header>
      <main>${slots.default}</main>
      <footer>${slots.footer}</footer>
    </div>`
}
```

## Conditional Slots {#conditional-slots}

Kabhi-kabhi aap chahte ho ki koi cheez tabhi render ho jab slot ke andar koi content pass kiya gaya ho.

Iske liye aap [$slots](/api/component-instance.html#slots) property ka use kar sakte ho, `v-if` directive ke saath combine karke.

Niche ke example mein hum ek `Card` component define kar rahe hain jisme teen conditional slots hain: `header`, `footer` aur ek `default` slot.

Jab bhi header / footer / default ke liye content diya gaya ho, hum uss content ko ek wrapper mein daal ke thoda aur styling provide karna chahenge:

```vue-html
<template>
  <div class="card">
    <div v-if="$slots.header" class="card-header">
      <slot name="header" />
    </div>

    <div v-if="$slots.default" class="card-content">
      <slot />
    </div>

    <div v-if="$slots.footer" class="card-footer">
      <slot name="footer" />
    </div>
  </div>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNqVVMtu2zAQ/BWCLZBLIjVoTq4aoA1yaA9t0eaoCy2tJcYUSZCUKyPwv2dJioplOw4C+EDuzM4+ONYT/aZ1tumBLmhhK8O1IxZcr29LyTutjCN3zNRkZVRHLrLcXzz9opRFHvnIxIuDTgvmAG+EFJ4WTnhOCPnQAqvBjHFE2uvbh5Zbgj/XAolwkWN4TM33VI/UalixXvjyo5yeqVVKOpCuyP0ob6utlHL7vUE3U4twkWP4hJq/jiPP4vSSOouNrHiTPVolcclPnl3SSnWaCzC/teNK2pIuSEA8xoRQ/3+GmDM9XKZ41UK1PhF/tIOPlfSPAQtmAyWdMMdMAy7C9/9+wYDnCexU3QtknwH/glWi9z1G2vde1tj2Hi90+yNYhcvmwd4PuHabhvKNeuYu8EuK1rk7M/pLu5+zm5BXyh1uMdnOu3S+95pvSCWYtV9xQcgqaXogj2yu+AqBj1YoZ7NosJLOEq5S9OXtPZtI1gFSppx8engUHs+vVhq9eVhq9ORRrXdpRyseSqfo6SmmnONK6XTw9yis24q448wXSG+0VAb3sSDXeiBoDV6TpWDV+ktENatrdMGCfAoBfL1JYNzzpINJjVFoJ9yKUKho19ul6OFQ6UYPx1rjIpPYeXIc/vXCgjetawzbni0dPnhhJ3T3DMVSruI=)

## Dynamic Slot Names {#dynamic-slot-names}

[Dynamic directive arguments](/guide/essentials/template-syntax.md#dynamic-arguments) `v-slot` ke saath bhi kaam karte hain, jisse aap dynamic slot names define kar sakte ho:

```vue-html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>

  <!-- shorthand ke saath -->
  <template #[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

Dhyan rahe ki yeh expression dynamic directive arguments ke [syntax constraints](/guide/essentials/template-syntax.md#dynamic-argument-syntax-constraints) ko follow kare.

## Scoped Slots {#scoped-slots}

Jaise ki humne [Render Scope](#render-scope) mein discuss kiya, slot content ko child component ke state ka access nahi hota.

Lekin kuch cases mein yeh useful ho sakta hai agar slot ka content parent aur child dono scopes ke data ka use kar sake. Iske liye hume ek aisa tarika chahiye jisse child slot render karte waqt data ko slot mein bhej sake.

Aur hum aisa kar bhi sakte hain — hum slot outlet ko attributes pass kar sakte hain bilkul waise hi jaise hum props component ko pass karte hain:

```vue-html
<!-- <MyComponent> template -->
<div>
  <slot :text="greetingMessage" :count="1"></slot>
</div>
```

Slot props ko receive karne ka tareeka thoda alag hota hai jab aap single default slot use kar rahe ho vs. named slots. Sabse pehle hum default slot ke saath prop receive karna dikhate hain, jisme `v-slot` directly child component ke tag par lagta hai:

```vue-html
<MyComponent v-slot="slotProps">
  {{ slotProps.text }} {{ slotProps.count }}
</MyComponent>
```

![scoped slots diagram](./images/scoped-slots.svg)

<!-- https://www.figma.com/file/QRneoj8eIdL1kw3WQaaEyc/scoped-slot -->

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNp9kMEKgzAMhl8l9OJlU3aVOhg7C3uAXsRlTtC2tFE2pO++dA5xMnZqk+b/8/2dxMnadBxQ5EL62rWWwCMN9qh021vjCMrn2fBNoya4OdNDkmarXhQnSstsVrOOC8LedhVhrEiuHca97wwVSsTj4oz1SvAUgKJpgqWZEj4IQoCvZm0Gtgghzss1BDvIbFkqdmID+CNdbbQnaBwitbop0fuqQSgguWPXmX+JePe1HT/QMtJBHnE51MZOCcjfzPx04JxsydPzp2Szxxo7vABY1I/p)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNqFkNFqxCAQRX9l8CUttAl9DbZQ+rzQD/AlJLNpwKjoJGwJ/nvHpAnusrAg6FzHO567iE/nynlCUQsZWj84+lBmGJ31BKffL8sng4bg7O0IRVllWnpWKAOgDF7WBx2em0kTLElt975QbwLkhkmIyvCS1TGXC8LR6YYwVSTzH8yvQVt6VyJt3966oAR38XhaFjjEkvBCECNcia2d2CLyOACZQ7CDrI6h4kXcAF7lcg+za6h5et4JPdLkzV4B9B6RBtOfMISmxxqKH9TarrGtATxMgf/bDfM/qExEUCdEDuLGXAmoV06+euNs2JK7tyCrzSNHjX9aurQf)

</div>

Child ke dwara slot mein pass kiye gaye props, `v-slot` directive ke corresponding value ke roop mein available hote hain, jise slot ke andar expressions se access kiya ja sakta hai.

Aap scoped slot ko ek function ki tarah samajh sakte ho jo child component ko pass kiya jata hai. Phir child component usse call karta hai aur props ko arguments ke roop mein pass karta hai:

```js
MyComponent({
  // default slot ko as a function pass kar rahe hain
  default: (slotProps) => {
    return `${slotProps.text} ${slotProps.count}`
  }
})

function MyComponent(slots) {
  const greetingMessage = 'hello'
  return `<div>${
    // slot function ko props ke saath call karo!
    slots.default({ text: greetingMessage, count: 1 })
  }</div>`
}
```

Ye kaafi close hai us tarike se jisme scoped slots compile hote hain, aur manual [render functions](/guide/extras/render-function) mein use kiye jaate hain.

Note karo ki `v-slot="slotProps"` slot function signature ko match karta hai. Bilkul function arguments ki tarah, hum `v-slot` mein destructuring ka bhi use kar sakte hain:

```vue-html
<MyComponent v-slot="{ text, count }">
  {{ text }} {{ count }}
</MyComponent>
```

### Named Scoped Slots {#named-scoped-slots}

Named scoped slots bhi isi tarah kaam karte hain - slot props `v-slot` directive ke value ke roop mein accessible hote hain: `v-slot:name="slotProps"`. Jab shorthand use karte hain, to ye kuch aisa dikhta hai:

```vue-html
<MyComponent>
  <template #header="headerProps">
    {{ headerProps }}
  </template>

  <template #default="defaultProps">
    {{ defaultProps }}
  </template>

  <template #footer="footerProps">
    {{ footerProps }}
  </template>
</MyComponent>
```

Named slot ko props pass karna:

```vue-html
<slot name="header" message="hello"></slot>
```

Dhyan rahe ki slot ka `name` props mein include nahi hota kyunki yeh reserved hota hai - to final `headerProps` hoga `{ message: 'hello' }`.

Agar aap named slots ko default scoped slot ke saath mix kar rahe ho, to aapko default slot ke liye explicit `<template>` tag ka use karna padega. Agar aap `v-slot` directive ko directly component par lagane ki koshish karenge to compilation error aayega. Ye isliye hota hai taaki default slot ke props ke scope ko lekar koi confusion na ho. Jaise ki:

```vue-html
<!-- <MyComponent> template -->
<div>
  <slot :message="hello"></slot>
  <slot name="footer" />
</div>
```

```vue-html
<!-- Ye template compile nahi hoga -->
<MyComponent v-slot="{ message }">
  <p>{{ message }}</p>
  <template #footer>
    <!-- message sirf default slot mein available hai, yahan nahi -->
    <p>{{ message }}</p>
  </template>
</MyComponent>
```

Default slot ke liye explicit `<template>` tag use karne se ye clear hota hai ki `message` prop doosre slot mein available nahi hai:

```vue-html
<MyComponent>
  <!-- Explicit default slot use karo -->
  <template #default="{ message }">
    <p>{{ message }}</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</MyComponent>
```

### Fancy List Example {#fancy-list-example}

Aap soch rahe honge ki scoped slots ka achha use case kya ho sakta hai. Yahan ek example hai: sochiye ek `<FancyList>` component hai jo items ki list render karta hai - ismein remote data load karne ka logic ho sakta hai, ya pagination/infinite scroll jaise features bhi. Lekin hum chahte hain ki har item ka layout parent component define kare jahan yeh component use ho raha hai. To usage kuch aisa hoga:

```vue-html
<FancyList :api-url="url" :per-page="10">
  <template #item="{ body, username, likes }">
    <div class="item">
      <p>{{ body }}</p>
      <p>by {{ username }} | {{ likes }} likes</p>
    </div>
  </template>
</FancyList>
```

Aur `<FancyList>` ke andar hum same `<slot>` ko multiple baar render kar sakte hain har item data ke saath (dhyan de ki hum `v-bind` ka use kar rahe hain object ko slot props ke roop mein pass karne ke liye):

```vue-html
<ul>
  <li v-for="item in items">
    <slot name="item" v-bind="item"></slot>
  </li>
</ul>
```

<div class="composition-api">

[Try it in the Playground](https://play.vuejs.org/#eNqFU2Fv0zAQ/StHJtROapNuZTBCNwnQQKBpTGxCQss+uMml8+bYlu2UlZL/zjlp0lQa40sU3/nd3Xv3vA7eax0uSwziYGZTw7UDi67Up4nkhVbGwScm09U5tw5yowoYhFEX8cBBImdRgyQMHRwWWjCHdAKYbdFM83FpxEkS0DcJINZoxpotkCIHkySo7xOixcMep19KrmGustUISotGsgJHIPgDWqg6DKEyvoRUMGsJ4HG9HGX16bqpAlU1izy5baqDFegYweYroMttMwLAHx/Y9Kyan36RWUTN2+mjXfpbrei8k6SjdSuBYFOlMaNI6AeAtcflSrqx5b8xhkl4jMU7H0yVUCaGvVeH8+PjKYWqWnpf5DQYBTtb+fc612Awh2qzzGaBiUyVpBVpo7SFE8gw5xIv/Wl4M9gsbjCCQbuywe3+FuXl9iiqO7xpElEEhUofKFQo2mTGiFiOLr3jcpFImuiaF6hKNxzuw8lpw7kuEy6ZKJGK3TR6NluLYXBVqwRXQjkLn0ueIc3TLonyZ0sm4acqKVovKIbDCVQjGsb1qvyg2telU4Yzz6eHv6ARBWdwjVqUNCbbFjqgQn6aW1J8RKfJhDg+5/lStG4QHJZjnpO5XjT0BMqFu+uZ81yxjEQJw7A1kOA76FyZjaWBy0akvu8tCQKeQ+d7wsy5zLpz1FlzU3kW1QP+x40ApWgWAySEJTv6/NitNMkllcTakwCaZZ5ADEf6cROas/RhYVQps5igEpkZLwzRROmG04OjDBcj7+Js+vYQDo9e0uH1qzeY5/s1vtaaqG969+vTTrsmBTMLLv12nuy7l+d5W673SBzxkzlfhPdWSXokdZMkSFWhuUDzTTtOnk6CuG2fBEwI9etrHXOmRLJUE0/vMH14In5vH30sCS4Nkr+WmARdztHQ6Jr02dUFPtJ/lyxUVgq6/UzyO1olSj9jc+0DcaWxe/fqab/UT51Uu7Znjw6lbUn5QWtR6vtJQM//4zPUt+NOw+lGzCqo/gLm1QS8)

</div>
<div class="options-api">

[Try it in the Playground](https://play.vuejs.org/#eNqNVNtq20AQ/ZWpQnECujhO0qaqY+hD25fQl4RCifKwllbKktXushcT1/W/d1bSSnYJNCCEZmbPmcuZ1S76olS6cTTKo6UpNVN2VQjWKqktfCOi3N4yY6HWsoVZmo0eD5kVAqAQ9KU7XNGaOG5h572lRAZBhTV574CJzJv7QuCzzMaMaFjaKk4sRQtgOeUmiiVO85siwncRQa6oThRpKHrO50XUnUdEwMMJw08M7mAtq20MzlAtSEtj4OyZGkweMIiq2AZKToxBgMcdxDCqVrueBfb7ZaaOQiOspZYgbL0FPBySIQD+eMeQc99/HJIsM0weqs+O258mjfZREE1jt5yCKaWiFXpSX0A/5loKmxj2m+YwT69p+7kXg0udw8nlYn19fYGufvSeZBXF0ZGmR2vwmrJKS4WiPswGWWYxzIIgs8fYH6mIJadnQXdNrdMiWAB+yJ7gsXdgLfjqcK10wtJqgmYZ+spnpGgl6up5oaa2fGKi6U8Yau9ZS6Wzpwi7WU1p7BMzaZcLbuBh0q2XM4fZXTc+uOPSGvjuWEWxlaAexr9uiIBf0qG3Uy6HxXwo9B+mn47CvbNSM+LHccDxAyvmjMA9Vdxh1WQiO0eywBVGEaN3Pj972wVxPKwOZ7BJWI2b+K5rOOVUNPbpYJNvJalwZmmahm3j7AhdSz3sPzDRS3R4SQwOCXxP4yVBzJqJarSzcY8H5mXWFfif1QVwPGjGcQWTLp7YrcLxCfyDdAuMW0cq30AOV+plcK1J+dxoXJkqR6igRCeNxjbxp3N6cX5V0Sb2K19dfFrA4uo9Gh8uP9K6Puvw3eyx9SH3IT/qPCZpiW6Y8Gq9mvekrutAN96o/V99ALPj)

</div>

### Renderless Components {#renderless-components}

Jo `<FancyList>` use case humne upar discuss kiya tha, usme reusable logic (jaise data fetching, pagination etc.) aur visual output dono ko encapsulate kiya gaya hai, jabki visual output ka ek hissa consumer component ko scoped slots ke through delegate kiya gaya hai.

Agar hum is concept ko thoda aur aage le jaayein, to hum aise components create kar sakte hain jo sirf logic ko encapsulate karte hain aur khud kuch render nahi karte - visual output poori tarah consumer component ke upar chhod diya jaata hai via scoped slots. Is tarah ke components ko hum **Renderless Component** kehte hain.

Ek example renderless component aisa ho sakta hai jo mouse ke current position ko track karne ka logic handle kare:

```vue-html
<MouseTracker v-slot="{ x, y }">
  Mouse is at: {{ x }}, {{ y }}
</MouseTracker>
```

<div class="composition-api">

[Playground mein try karo](https://play.vuejs.org/#eNqNUcFqhDAQ/ZUhF12w2rO4Cz301t5aaCEX0dki1SQko6uI/96J7i4qLPQQmHmZ9+Y9ZhQvxsRdiyIVmStsZQgcUmtOUlWN0ZbgXbcOP2xe/KKFs9UNBHGyBj09kCpLFj4zuSFsTJ0T+o6yjUb35GpNRylG6CMYYJKCpwAkzWNQOcgphZG/YZoiX/DQNAttFjMrS+6LRCT2rh6HGsHiOQKtmKIIS19+qmZpYLrmXIKxM1Vo5Yj9HD0vfD7ckGGF3LDWlOyHP/idYPQCfdzldTtjscl/8MuDww78lsqHVHdTYXjwCpdKlfoS52X52qGit8oRKrRhwHYdNrrDILouPbCNVZCtgJ1n/6Xx8JYAmT8epD3fr5cC0oGLQYpkd4zpD27R0vA=)

</div>
<div class="options-api">

[Playground mein try karo](https://play.vuejs.org/#eNqVUU1rwzAM/SvCl7SQJTuHdLDDbttthw18MbW6hjW2seU0oeS/T0lounQfUDBGepaenvxO4tG5rIkoClGGra8cPUhT1c56ghcbA756tf1EDztva0iy/Ds4NCbSAEiD7diicafigeA0oFvLPAYNhWICYEE5IL00fMp8Hs0JYe0OinDIqFyIaO7CwdJGihO0KXTcLriK59NYBlUARTyMn6Hv0yHgIp7ARAvl3FXm8yCRiuu1Fv/x23JakVqtz3t5pOjNOQNoC7hPz0nHyRSzEr7Ghxppb/XlZ6JjRlzhTAlA+ypkLWwAM6c+8G2BdzP+/pPbRkOoL/KOldH2mCmtnxr247kKhAb9KuHKgLVtMEkn2knG+sIVzV9sfmy8hfB/swHKwV0oWja4lQKKjoNOivzKrf4L/JPqaQ==)

</div>

Yeh ek interesting pattern hai, lekin jitna kuch Renderless Components se achieve kiya ja sakta hai, wo Composition API se aur bhi efficient tareeke se kiya ja sakta hai, bina extra component nesting ka overhead laaye. Baad mein hum dekhenge ki kaise wahi mouse tracking functionality ko ek [Composable](/guide/reusability/composables) ke roop mein implement kiya ja sakta hai.

Lekin, scoped slots ab bhi useful hain un cases mein jahan hume logic ko encapsulate karna hota hai **aur** visual output ko bhi compose karna hota hai, jaise ki `<FancyList>` example mein.
