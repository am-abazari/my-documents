---
sidebar_position: 1
---

# ویژگی‌ها

ویژگی‌های `js` مانند spread operators, async await, destructuring, ...


<hr />

## Destructuring
زمانی که یک آبجکت داریم و نیاز به یک مقدار خاص داخلش داریم میتونیم از destructuring استفاده بکنیم


```js {12}
const data = {
    id: 13,
    name :"amir",
    age : 18,
    address:{
        city: "Tehran",
        street:"golestan 10",
        postalCode: 1549871134
    }
}

const {id, name, adress:{city , postalCode}} = data
```

در این عملیات مقدارهای `id`, `name`, `city`, `postalCode`, `address` از داخل آبجکت `data` بدست می‌آیند و به عنوان یک متغیر با همین اسم‌ها ذخیره میشوند
برای تغییر اسم یک چیز به طور خلاصه باید از سینتکس زیر استفاده کرد

```js
const {name: firstName, age} = data
```
که اسم `name` رو به صورت `firstName` ذخیره میکند



<hr />

## Spread Operators

فرض کن یک آبجکت(آرایه) داریم، میخوایم مقادیر قبلی اون آبجکت(آرایه) رو نگه داریم و صرفا یک مقدارش رو عوض کنیم یا بهش اضافه کنیم. در این حالت باید از این ویژگی استفاده کرد

```js title="array.js"
const nums = [1, 3, 10, 14]
const newNums = [...nums, 18, 13] // [1, 3, 10, 14, 18, 13]
```

<br />

```js title="object.js"
const user = {
    id: 12,
    name: "ali",
    age: 21
}
const newUser = {
    ...user,
    age: 23,
    petName: "tala"
} // { id: 12, name:"ali", age:23, petName:"tala" }
```

<hr />

## Async | Await

با مفهوم promise ها قطعا آشنا هستی
این یک سینتکس راحتتره

```js title="create.js"
const fetchData = async (id) => {
    const response = await fetch(`example.com/${id}`)
    const data = await response.json()
    return data;
}
```

هرجا `await` داشتی باید تابعی که داخلش هست `async` بشه و از طرفی وقتی میخوای از اون تابع استفاده کنی باید پشتش `await` بزنی.

```js title="use.js"
const main = async () =>{
    const data = await fetchData(3);
    console.log(data)
}
```

<hr />

## Export | Import

### Export

```js {15-16} title="user.js"
const userInfo = {
    id: 12,
    name: "Ali",
    username: "Hosseini"
}
const userAddres = {
    postalCode: 125661313,
    street: "golestan 9"
}
const user = {
    userInfo,
    userAddres
}

export {userInfo, userAddres}
export default user
```

توی حالت  `export {...}` به صورت named export میشود و در صورت `export default ...` به صورت دیفالت export میشود

### Import

```js
import user, {userInfo, userAddress} from "./user.js";
```

`import {...}` مربوط به export های named است و `import ...` مربوط به export default است