# Vue Application Banana {#creating-a-vue-application}

## Application Instance kya hoti hai {#the-application-instance}

Har Vue application ki shuruaat ek naye **application instance** create karne se hoti hai,  
jo ki [`createApp`](/api/application#createapp) function ke through kiya jaata hai:

```js
import { createApp } from 'vue'

const app = createApp({
  /* root component options */
})
```

## Root Component {#the-root-component}

Jo object hum `createApp` mein pass kar rahe hain, wo actually ek component hota hai.  
Har app mein ek "root component" hona zaroori hota hai, jo baaki components ko apne children ke roop mein contain karta hai.

Agar aap Single-File Components ka use kar rahe ho,  
toh root component ko aam taur par kisi alag file se import kiya jaata hai:

```js
import { createApp } from 'vue'
// Root component App ko ek single-file component se import kar rahe hain.
import App from './App.vue'

const app = createApp(App)
```

Is guide mein diye gaye kai examples sirf ek hi component ka use karte hain,  
lekin jyadatar real applications ek component tree ke form mein organized hote hain —  
jahaan kai nested aur reusable components hote hain.

Maan lijiye ek Todo application hai, uska component tree kuch is tarah ka ho sakta hai:

```

App (root component)
├─ TodoList
│ └─ TodoItem
│ ├─ TodoDeleteButton
│ └─ TodoEditButton
└─ TodoFooter
├─ TodoClearButton
└─ TodoStatistics

```

Guide ke aage ke sections mein hum yeh dekhenge ki multiple components ko kaise define aur compose kiya jaata hai.  
Lekin usse pehle hum yeh samjhenge ki ek single component ke andar kya hota hai.

## App ko Mount Karna {#mounting-the-app}

Ek application instance tab tak kuch render nahi karta jab tak uska `.mount()` method call na kiya jaye.  
Yeh method ek "container" argument expect karta hai,  
jo ya to ek actual DOM element ho sakta hai ya fir ek selector string:

```html
<div id="app"></div>
```

```js
app.mount('#app')
```

App ka root component jo bhi content render karega, wo container element ke andar dikhai dega.  
Lekin container element khud app ka part nahi mana jaata.

`.mount()` method ko hamesha tab call karna chahiye jab app ki saari configurations  
aur asset registrations complete ho chuki ho.  
Yeh bhi dhyan rahe ki `.mount()` ka return value — asset registration methods ke alag —  
application instance nahi, balki root component instance hota hai.

### In-DOM Root Component Template {#in-dom-root-component-template}

Root component ka template aam taur par component ke andar hi likha hota hai,  
lekin aap chaho to us template ko alag se bhi de sakte ho  
— seedha mount container ke andar likh kar:

```html
<div id="app">
  <button @click="count++">{{ count }}</button>
</div>
```

```js
import { createApp } from 'vue'

const app = createApp({
  data() {
    return {
      count: 0
    }
  }
})

app.mount('#app')
```

Agar root component ke paas `template` option nahi hai,  
toh Vue automatically container element ke `innerHTML` ko template ke roop mein use karega.

In-DOM templates aksar un applications mein use ki jaati hain jo [Vue ko bina build step ke use kar rahe hote hain](/guide/quick-start.html#using-vue-from-cdn).  
Yeh server-side frameworks ke saath bhi use ki ja sakti hain,  
jahan root template dynamically server ke through generate hoti hai.

## App Configurations {#app-configurations}

Application instance ek `.config` object expose karta hai  
jiske through hum kuch app-level options configure kar sakte hain —  
jaise ek app-level error handler define karna jo sabhi descendant components se aane wale errors ko capture kare:

```js
app.config.errorHandler = (err) => {
  /* handle error */
}
```

Application instance kuch methods bhi provide karta hai jinse hum app-scoped assets register kar sakte hain. Jaise ki, ek component register karna:

```js
app.component('TodoDeleteButton', TodoDeleteButton)
```

Isse `TodoDeleteButton` component poore app mein kahin bhi use kiya ja sakta hai.  
Components aur doosre asset types ka registration guide ke aage ke sections mein detail mein explain kiya gaya hai.  
Aap application instance ke sabhi APIs ki full list yahan dekh sakte ho: [API reference](/api/application)

Yaad rahe: App ko mount karne se pehle sabhi configurations apply kar lena chahiye!

## Multiple Application Instances {#multiple-application-instances}

Aap ek hi page par sirf ek application instance tak simit nahi ho.  
`createApp` API allow karta hai ki ek hi page par multiple Vue applications ho sakti hain —  
aur har ek ka apna configuration scope aur global assets ho sakta hai:

```js
const app1 = createApp({
  /* ... */
})
app1.mount('#container-1')

const app2 = createApp({
  /* ... */
})
app2.mount('#container-2')
```

Agar aap Vue ka use server-rendered HTML ko enhance karne ke liye kar rahe ho aur sirf kisi bade page ke kuch specific parts ko hi Vue se control karna hai,toh poore page par ek hi Vue application instance mount karne se bachein.

Iske bajaye, chhoti-chhoti multiple application instances banayein aur unhe un specific elements par mount karein jinke liye wo responsible hain.
