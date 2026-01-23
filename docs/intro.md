---
sidebar_position: 1
---

# شروع

اینجا میتونی تمام چیزایی که در روند یادگیریم به نظرم مهم اومدن و بعدا به دردم میخورن یا تجربیاتم رو بخونی !

<hr/>


## سایت‌های مهم

### غلط املایی
[emla.virastaran.net](https://emla.virastaran.net/)

### آیکون‌
[icones.js.org](https://icones.js.org/)

### کران‌های CSS (clamp)
[clamp.font-size.app](https://clamp.font-size.app/)

### عکس‌های Illustrator طور
[freepik.com](https://freepik.com/)

### ساخت فایل `.gitignore`
[gitignore.io](https://www.toptal.com/developers/gitignore)



## اصطلاح‌ها و موارد مهم

### Node چیست ؟
موتور جستجویی که توسعه پیدا کرده تا کد‌های `javascript` رو بتونه توی بکند هم اجرا بکنه، مثل موتور جستجوی V8 گوگل و یا ...

### Boiler Plate چیست؟
اصطلاح  `boiler plate` به معنی تکرار کد به صورت یکسان هست.
برای مثال : 

```js
const males = [
    {
        name: "ali",
        age: 14
    },
    {
        name: "hossen",
        age: 12
    }
]
const females = [
    {
        name: "mina",
        age: 50
    },
    {
        name: "hoda",
        age: 22
    }
]

const C = A.sort((a, b) => {
    if (a.age !== b.age) return 0;  
    if (a.name !== b.name) return a.age - b.age;
    return a.age.localeCompare(b.age);
})
const D = B.sort((a, b) => {
    if (a.age !== b.age) return 0;
    if (a.name !== b.name) return a.age - b.age;
    return a.age.localeCompare(b.age);
})
```

در این حالت همانطور که مشاهده میشود تابع مقایسه چندین بار صدا شده درحالی که کدی کاملا ثابت داره.
اگر تعداد sort ها بیشتر میشد این تابع چندین بار دیگه به صورت تکراری تکرار میشد
که این درواقع همان `boiler plate` است
که برای جلوگیری ازش باید بخش تکراری را `reusable` بکنیم

```js
const males = [
    {
        name: "ali",
        age: 14
    },
    {
        name: "hossen",
        age: 12
    }
]
const females = [
    {
        name: "mina",
        age: 50
    },
    {
        name: "hoda",
        age: 22
    }
]

const compare = (a,b) => {
    if (a.age !== b.age) return 0;
    if (a.name !== b.name) return a.age - b.age;
    return a.age.localeCompare(b.age);
}
const C = A.sort(compare);
const D = B.sort(compare);
```