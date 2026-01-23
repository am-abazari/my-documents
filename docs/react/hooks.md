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