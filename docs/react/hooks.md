---
sidebar_position: 3
---
# Hooks
در اینجا انواع مختلف hook های ری‌اکت و تعریفشون رو داریم

## تعریف
 hook به معنی قلاب هست. از ورژن `16.8` به بعد ری‌اکت همراه با `functional components` معرفی شده. اکثر hook ها با عبارت `use` شروع میشوند و وظایف مختلفی دارند
 

## useState


این hook در صورت تغییر باعث رندر مجدد کل کامپوننتی که داخلش هستیم میشود. اما با هربار تغییر مقدار خودش ریست نمیشود. بلکه صرفا DOM مجدد رندر میشود و مقدار خودش حفظ میشود

```jsx {2}
const state = useState(initialValue) // initialValue میتونه هر دیتا تایپی باشه
constole.log(state) // [initialValue, Fn]
state[1](newValue) // state = [newValue, Fn]
```

همونطور که قابل مشاهده هست خود `state` شامل یک آرایه با ۲ عضو هست. عضو اول مقدار حال حاضر این `state` را نشان میدهد و عضو دوم تابع تغییر این `state` است

عضو دوم آرایه وظیفه‌ی تغییر عضو اول رو داره. نکته این هست که با هربار تغییر عضو اول DOM مجدد رندر میشود

#### Better Syntax
سینتکس معمول و بهتری که برای تعریف `useState` استفاده میکنند با استفاده از destructuring است
```jsx
const [state, setState] = useState(initialValue)
console.log(state) // initialValue
```


### Closure in States

ما اگر ۳ بار پشت سر هم مقدار setState را کال کنیم فقط یکی از آنها اجرا میشود.

دلیل این فرآیند این هست که ساختار state یک ساختار Closure هست و فقط به متغیرهای درونی خودش دسترسی دارد.

برای رفع این مشکل باید از `callback function` درون `setState` استفاده کرد.

یعنی مقدار قبلی `state` را دریافت کرد و تغییرات را مستقیم روی آن اعمال کرد.

```jsx {9}
const [count, setCount] = useState(0);

const increaseBy3 = () =>{
    setCount(count + 1)
    setCount(count + 1)
    setCount(count + 1)
}

console.log(count) // 1
```

همانطور که مشاهده میشود صرفا یکبار مقدار `count` اشافه شده که این به این دلیل است که هربار react تابع `setCount` را کال میکند ب مقدار درونی Closure count دسترسی دارد که برابر ۰ است

:::info
برای حل این اشکال باید `setState` مدنظر را به صورت یک callback function کال کرد که بتوان مقدار حاضر حاظر state را دریافت کرد

هرمقداری که callback function برگردونه (return) مقدار جدید state میشود
:::

```jsx {9}
const [count, setCount] = useState(0);

const increaseBy3 = () =>{
    setCount((prevCount) => (prevCount + 1))
    setCount((prevCount) => (prevCount + 1))
    setCount((prevCount) => (prevCount + 1))
    
}
increaseBy3() // 3
```

#### یک مثال پیچیده

یک `id`, `value` میگیره و مقدار سن کاربر متناظر با `id` رو به مقدار `value` تا اضافه میکنه

```jsx {17-21}
const [users, setUsers] = useState([
    {
        id: 12,
        name: "amirhossein",
        lastname: "abazari",
        age: 20
    },
    {
        id: 13,
        name: "ali",
        lastname: "abazari",
        age: 45
    }
    // ...
])

const increaseAge = (id, value) => {
    setUsers((prevUsers) => {
        return prevUsers.map(user => (user.id == id ? { ...user, age: user.age + value } : user))
    })
}

increaseAge(12, 4)
/*
{
        id: 12,
        name: "amirhossein",
        lastname: "abazari",
        age: 24
    },
    {
        id: 13,
        name: "ali",
        lastname: "abazari",
        age: 45
    }
    // ...
 */
```


## useEffect

- این هوک برای بررسی تغییرات state های صفحه‌ی ما استفاده میشود

- به عبارت دیگر اگر state مدنظر ما تغییر بکند useEffect مرتبط به آن کال میشود

- useEffect شامیل یک Life Cycle ۳ مرحله‌ای است

### Life Cycle

1. Mount : برای اولین بار که صدا زده میشود

2. Update : هربار که state مدنظر ما تغییر میکند

3. Unmount
    1. زمانی اتفاق می‌افتد که کامپوننت مدنظر صفحه حذف شود یا دیگر render نشود
    2. قبل از هربار انجام عملیات update درون lifecycle یکبار مقدار unmount صدا زده میشود 

### How to Use

```jsx {6,9}
import { useEffect } from "react"

const Component = () =>{
   useEffect(() => {

      // 1

      return () => {
         // 2
      }
   }, [updateListener])
   
   return (<>...</>)
}
```

بخش ۱ برای اولین بار هنگام mount کامپوننت صدا زده میشود

پس از mount اولیه هربار که `updateListener` تغییر بکند ابتدا بخش ۲ و سپس بخش ۱ صدا زده میشود.

پس درکل روند تغییرات `useEffect` مانند نمونه‌ی زیر است

1. Mount - بخش ۱
2. تغییر `updateListener`
3. Unmount - بخش ۲
4. Updating Mount - بخش ۱
5. تغییر `updateListener`
6. ... 


:::info
اگر جای updateListener هیچ چیزی قرار ندیم صرفا هنگام mount کل کامپوننت این `useEffect` کال میشود

یعنی تنها وابسته به کل کامپوننت و رندر اون هست
:::

### Unmount
:::info
به Unmount useEffect عبارت clean up هم استفاده میشود
:::

از unmount زمانی استفاده میشود که کامپوننت از حالت رندر دربیاد، وارد صفحه‌ی دیگری بشیم و یا قبل از هربار update شدن

یک مثال کاربردی از useEffect Unmount

برای مثال فرض کن با یک input داریم که هربار کاربر مقدار داخلش رو عوض میکنه یک ریکوست به سرور ما میفرستیم. در این حالت ممکنه خیلی از ریکوست‌ها بیهوده باشه

در این حالت ما ریکوست قبلی کاربر رو هنگام آپدیت یک state ایگنور و abort میکنیم

```jsx {9,11-13}
import {useState, useEffect} from "react"

const Component = () => {
   const [id, setID] = useState(null);
   
   const controller = new AbortController();
   
   useEffect(() => {
      fetch(`url/${id}`, {signal:controller.signal})
      
      return () =>{
          controller.abort()
      }
   }, [id]);

   return (
           <input type="number" value={id} onChange={(e) => {
              setID(e.target.value)
           }}/>
   )
}
```

توی این نمونه ما یک `signal` به درخواست `fetch` اختصاص میدیم و قبل از اجرای هر آپدیت میدانیم تابع unmount صدا میشود.
پس قبل از ارسال درخواست جدید درخواست قبلی را `abort` میکنیم

این باعث میشه لود روی سرور خیلی کمتر بشه.


## useReducer

یکی از `state` های ری‌اکت هست که تغییرش باعث رندر مجدد کامپوننت میشه اما فرمت و عملکردش متفاوت از `useState` هست

```jsx
const [state, dispatch] = useReducer(reducer, initialState)
```

تعریف یک useReducer مانند نمونه‌ی بالا هست. که با `state` میتونیم مقدار حال حاضر رو بخونیم.

`dispatch` یک `action` دریافت میکند و مقدار قبلی `state` را به همراه این `action` به تابع `reducer` پاس میدهد و تابع `reducer` باتوجه به نوع `action` تغییرات را اعمال میکند

هرمقداری که تابع `reducer` برگردونه (return بکنه) درون `state` قرار میگیره.

`initialState` هم مقدار اولیه‌ی این `state` است که هر دیتاتایپی میتواند باشد

### Implementation

فرض کن یک `counter` داریم که ۳ عمل میتونیم روش انجام بدیم. ۱. افزایش سه واحدی ۲. کاهش پنج واحدی ۳. صفر کردن `counter`

```jsx
import { useReducer } from "react";

const reducer = (state, action) =>{
    switch (action.type){
       case ("increase") :
           return state + action.payload
       case ("decrease") :
           return state - action.payload
       case ("reset") :
           return 0
    }
}

const Component = () =>{
    const [counter, dispatch] = useReducer(reducer, 0)
    
   return(
       <div>
          <p>{counter}</p>
          <button onClick={()=>{dispatch({type : "increase", payload:3})}}>Increase 3</button>
          <button onClick={()=>{dispatch({type : "decrease", payload:5})}}>Decrease 5</button>
          <button onClick={()=>{dispatch({type : "reset"})}}>Reset</button>
       </div>
    )
}
```

روند اجرای `useReducer` به این صورت است که ابتدا تابع `dispatch` با یک ورودی به نام `action` کال میشود که معمولا فرمت ورودی استاندارد `dispatch` مانند شکل زیر است

```json
{
   "type": "...",
   "payload": {
    /* ... */
   }
}
```
پس از کال کردن `dispatch` مستقیما مقدار `action` و `state` حال حاضر به تابع `reducer` پاس داده میشود.

حال ما به ازای `action` های متفاوت در `dispatch` باید در تابع `reducer` اونارو هندل کنیم که معمولا با `switch case` این اتفاق می‌افتد

درنهایت هرمقداری که توسط تابع `reducer` برگردانده شود به عنوان مقدار جدید `state` قرار میگیرد


### تفاوت useReducer و useState

useReducer مفاهیمی مانند `dispatch` کردن را برای حالات مختلف دارد درحالی که مفهومی پشت `setState` لزوما نیست

#### چه زمانی از useReducer به جای useState استفاده کنیم؟ 

1. تعداد `action` ها خیلی زیاد باشد یا شباهت بسیار زیادی باهم داشته باشن
2. برای api ها مناسب هست. چون میتونیم یک `action` برای ارور داشته باشیم و ارورهندلینگ راحتتر باش
3. دیتاها سنگین باشن و نیاز به تعداد زیادی `useState` داشته باشیم درحالی که `reducer` همه تا حد خوبی یکسان است