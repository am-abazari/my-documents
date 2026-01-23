---
sidebar_position: 2
---

# نکات و آموزش‌ها
اینجا نکته‌های ری‌اکت رو بهشون اشاره کردم، تریک‌هایی که میشه استفاده کرد و مواردی که به نظرم مهم اومدن

## React Fragment
گاهی وقتا میخوایم چندتا `div` کنار هم قرار بدیم اما چون همواره `return (...)` باید یک مقدار را داخلش برگردونه پس برگردوندن چند `div` کنار هم غیر ممکن میشود
اینجا میتونیم از `React.Fragment` استفاده بکنیم

```jsx
import React from "react"

const Component = () =>{
    return(
        <React.Fragment>
            ...
        </React.Fragment>
    )
}
```

به جای `<React.Fragment>` میتونیم از `<>` استفاده بکنیم

```jsx
import React from "react"

const Component = () =>{
    return(
        <>
            ...
        </>
    )
}
```


## Conditional Rendering
برای مثال وقتی یک `state` لود شده باید مقدار اون `state` رو نشون بدیم و وقتی لود نشده باید عبارت `loading` رو نشون بدیم

۳ روش برای پیاده‌سازی این عملکرد وجود داره.

### Normal Condition

```jsx
import { useState } from "react"

const Component = () =>{
    const [loading, setLoading] = useState(0);
    
    if(loading == 0){
        return (<p>Loading ...</p>)
    }else {
        return (<p>Data Loaded Successfully !</p>);
    }
}
```

### Ternery Operators

```jsx
import { useState } from "react"

const Component = () =>{
    const [loading, setLoading] = useState(0);

    return (
        <>
            {
                loading ? (<p>Loading ...</p>) :(<p>Data Loaded Successfully ! </p>)
            }    
        </>
    )
}
```

- نکته : درحالت کلی برای ternery operators اگر به صورت if else بود باید `condition ? ... : ...` نوشت و اگر فقط if بود و else شرط مهم نبود میتوان `condition && ...` نوشت

### Hidden Style
:::danger
این بدترین حالت هست چون DOM مجبور هست المنت رو رندر بکنه و بهش استایل بده
:::

```jsx
import { useState } from "react"

const Component = () =>{
    const [loading, setLoading] = useState(0);

    return (
        <>
            <p style={{display: !loading && "none"}}>Loading ...</p>
            <p style={{display: loading && "none"}}>Data Loaded Successfully ! </p>
        </>
    )
}
```


## Key Render
توی ری‌اکت شاید فکر کنی key prop ای که پاس میدی مهم نباشه. اما خیلی مهمه !

دلیلش اینه که ری‌اکت به هر المنت توی صفحه یک unique key میده و هروقت بخواد اون المنت رو روش تغیری اعمال بکنه خیلی سریعتر `O(log(n))` میتونه پیداش بکنه و اگر این کلید رو بهش اختصاص ندی باید `O(n)` عملیات برای پیدا کردنش صرف بکنه.
پس حتمی همیشه به المنت‌هایی که رندر میکنی مثلا توی `map`, `filter`, ... یک کلید یونیک بده

### JSON ID
اگر دیتا رو داری از api میگیری به ازای هر آیتم که رندر میکنی `key` اون رو برابر `id` اون آیتم توی دیتای‌ `json` که از api میاد قرار بده

```jsx
const data = [
    {
        id: "314@75641scdeah312",
        "name": "ali",
        // ...
    },
    {
        id: "isn14@56dsdh31ljd",
        "name": "amir",
        // ...
    }, 
    // ...
]
return (
    <>
        { data.map(item=>(<p key={item.id}>{item.name}</p>)) }
    </>
)
```

### UUID
یک کتابخونه‌ی رایگان برای جنیرت کردن عبارت‌های یونیک و یکتا است

```jsx
import { v4 as uuidv4 } from "uuid";

const data = [
    {
        "name": "ali",
        // ...
    },
    {
        "name": "amir",
        // ...
    },
    // ...
]
return (
    <>
        { data.map(item=>(<p key={ uuidv4() }>{item.name}</p>)) }
    </>
)
```

## Props

### تعریف

این آپشن برای پاس دادن مقادیری از کامپوننت پدر به کامپوننت فرزند است.

```jsx title="Parent.jsx"
const Parent = () =>{
    return(
        <Child data={...} id={...} className={...} >
            <div>
                <h1>children prop</h1>
            </div>
        </Child>
    )    
}
```

درواقع در این نمونه به کامپوننت فرزند ۴ مقدار `data`, `id`, `className`, `children` پاس داده شده است که prop مربوط به `children` همان مقادیری است که درون کامپوننت `<Child>` قرار دارد

```jsx title="Child.jsx"
const Child = ({data, children, ...props}) =>{
    const [user, setUser] = useState(data);
    return(
        <div {...props}>
            {children}    
        </div>
    )    
}
```
در این مثال ما به طور مستقیم از بین prop های پاس داده شده مقادیر `data` , `children` رو دریافت کردیم و باقی مقادیر رو به صورت `...props` spread operator کردیم. یعنی عملا المنت `div` اتریبیوت `id` و `className` را دریافت میکند 

از طرفی ما از `data` به طور مستقیم در یک `useState` به دلخواه استفاده کردیم.

بدیهی است در این مثال از روش props destructuring استفاده کردیم. ما میتونیم به طور مستقیم ابتدا `props` رو بگیریم و هرجا دلمون خواست از `props.data` یا `props.id` و ... استفاده کنیم اما در اینجا آنها را destructure کردیم

### Props Passing

در پاس دادن `prop` ما میتونیم انواع مختلف `prop` مانند توابع، متغیرها، کامپوننت و ... پاس داد

#### Pass Function
میتونیم انواع توابع رو به عنوان `prop` پاس بدیم. یکی از متداول ترینشون تابع `setState` است

```jsx title="Parent.jsx" {1,5,9}
const increaseFunction = (value) =>{
    // ...
}

const [count, setCount] = useState(10);

const Parent = () =>{
    return(
        <Children increase={increaseFunction} setCount={setCount} />
    )
}
```

```jsx title="Children.jsx" {3,4}
const Children = ({ increase, setCount }) =>{
    
    increase(12);
    setCount(18)
    
    return (
        <> ... </>
    )
}
```

#### Pass JSX or Component

ما به راحتی میتونی المنت‌ها، کامپوننت‌ها و... رو به صورت `prop` پاس بدیم

```jsx title="Parent.jsx" {1,5,9}
import OtherComponent from "../components/OtherComponent"

const Parent = () =>{
    return(
        <Children ShowComponent={OtherComponent} Message={<p>successfully created OtherComponent</p>} />
    )
}
```

```jsx title="Children.jsx" {5,4}
const Children = ({ ShowComponent, Message }) =>{
    return (
        <> 
            <ShowComponent />
            <Message />
            ...
        </>
    )
}
```


### Props Drilling
مفهوم پاس دادن یک `prop` به چندین لایه کامپوننت پایینتر هست.

برای مثال اگر `counter` در کامپوننت پدر وجود داشته باشد و آنرا به کامپوننت فرزند پاس دهد، سپس کامپوننت فرزند به کامپوننت نوه پاس دهد، و همینطور این `prop` پایینتر برود یعنی عملیات props drilling اتفاق افتاده که از دریل کردن و پایین رفتن میاد

```
Parent.jsx
Children.jsx
...
ChldrensChildren.jsx
```

```jsx title="Parent.jsx"
import Children from "./Children"

const Parent = () =>{
    return(
        <Children counter={12} />
    )
}
```

```jsx title="Children.jsx"
import ChildrensChildren from "./ChildrensChildren"

const Children = ({counter}) => {
    return(
        <ChildrensChildren counter={counter} />
    )
}
```

```jsx title="ChildrensChildren.jsx"
const ChildrensChildren = ({counter}) =>{
    return(
        <p>{counter}</p>
    )
}
```

این مفهوم props drilling است

برای حل این مشکل چند روش وجود دارد که بهترین و اصلی ترین آنها استفاده از state manager ها مانند `zustand`, `redux`, `context` و ... است

اما یک روش بدون نیاز به state manager ها اینجا اشاره میکنیم
<br />
#### Component Composition
در این روش بدون استفاده از state manager مشکل props drilling را تا حدی حل میکنیم

به اینصورت که مستقیم فرزندی که از prop مدنظر ما استفاده میکند را درون `Parent.jsx` قرار میدهیم !

```jsx title="Parent.jsx" {6-8}
import Children from "./Children"
import ChildrensChildren from "./ChildrensChildren"

const Parent = () =>{
    return(
        <Children>
            <ChildrensChildren counter={12} />
        </Children>
    )
}
```

```jsx title="Children.jsx"
const Children = ({children}) => {
    return(
        <>
            {children}
        </>
    )
}
```

```jsx title="ChildrensChildren.jsx"
const ChildrensChildren = ({counter}) =>{
    return(
        <p>{counter}</p>
    )
}
```

درواقع در این روش تنها یکبار prop مدنظر را به کامپوننتی که ازش استفاده میکنه پاس میدهیم !