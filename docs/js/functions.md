---
sidebar_position: 1
---

# توابع

توابع مهم `js` از جمله map, filter, find, reduce, ...

<hr />

## Arrow Functions
در `ES2016` این آپشن به جاواسکرپیت اضافه شده که به جای نوشتن کد کامل یک تابع اون رو با فلش نمایش میدهیم

```js
const arrowFunction = (inputs) =>{
    // your code
    
    return (outputs)
}
```

<hr />

## Callback Functions

توابعی که ورودی آنها یک تابع است مانند useEffect در react.js


```js

const callbackFunction = (callback, a, b) => {
    if (!a || !b) return 0;
    return callback(a, b);
}

console.log(callbackFunction((a, b) => (a + b), 10, 11)) // 21
console.log(callbackFunction((a, b) => (a - b), 20, 11)) // 9

```
<hr />

## Map
روی یک آرایه پیمایش میکنه و نکته‌ی مهم این هست که بعد از اتمام پیمایش یک آرایه‌ی جدید برمیگردونه

```js
const numbers = [12, 10, 3, 4, 7, 8, 13]
const newNumbers = numbers.map((number, index) => number * index); // [0, 10, 6, 12, 28, 40, 78]
```


<hr />

## Filter
روی یک آرایه پیمایش میکنه و نکته‌ی مهم این هست که بعد از اتمام پیمایش یک آرایه‌ی جدید برمیگردونه
و مقادیری که از شرط گذر کنن باقی میمونن
یعنی در شرط filter باید صدق کنند
و نکته ای که هست اینه که اگر به ازای هر عضو آرایه شرط برقرار باشه مقدار همون عضو رو برمیگردونه
```js
const numbers = [12, 10, 3, 4, 7, 8, 13]
const newNumbers = numbers.filter((number, index) => number * index > 8); // [10, 4, 7, 8, 13]
```

<hr />

## Find
روی یک آرایه پیمایش میکنه و نکته‌ی مهم این هست که بعد از اتمام پیمایش یک آرایه‌ی جدید برمیگردونه
تنها تفاوت find با filter این هست که صرفا اولین مقداری که پیدا بکنه رو برمیگردونه. نه آرایه‌ای از اون مقادیر
```js
const numbers = [12, 10, 3, 4, 7, 8, 13]
const newNumbers = numbers.filter((number, index) => number * index > 8); // 10
```

<hr />

## Reduce
وظیفه‌ی این تابع این هست که تمام مقادیر یک آرایه رو کاهش بده به یک چیز و خروجیش یک عضو هست
دو مقدار `accumulator` و `currentValue` میگیره که `accumulator` مقدار نهایی رو نگه میداره و هر تغییری باید روی این اعمال بشه.

نکته: توی خط آخر بخش هایلایت شده مقدار 0 مقدار اولیه‌ی `accumulator` است
```js {4}
const numbers = [12, 10, 3, 4, 7, 8, 13]
const sum = numbers.reduce((accumulator, currentValue) => {
    return accumulator + currentValue;
}, 0); // 57 = 12 + 10 + 3 + 4 + 7 + 9 + 13
```