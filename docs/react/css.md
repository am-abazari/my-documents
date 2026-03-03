---
sidebar_position: 4
---

# Styles | CSS

نحوه‌ی استایل‌دهی به react و کامپوننت‌ها با استفاده از css module, css, styled components

## CSS Manual Import

در این روش مستقیما فایل `css` میسازیم و در کامپوننت یا صفحه‌ی مدنظر import میکنیم

```
Cards.jsx
Cards.css
```

دو فایل `Cards.jsx` و `Cards.css` در کنار هم قرار میدهیم

```css title="Cards.css"
.card {
  display: grid;
}
.title {
  font-size: 20px;
  font-weight: 700;
}
/* ... */
```

```jsx {1} title="Cards.jsx"
import "./Cards.css";

const Cards = () => {
  return (
    <div className="card">
      <h2 className="title">card title</h2>
      <p className="description">card description ...</p>
    </div>
  );
};
```

مشکلی که این روش دارد این هست که ممکن است کلاس `card` در بقیه کامپوننت‌ها هم وجود داشته باشد و در این صورت این استایل‌ها به آنها هم اعمال میشود !

## CSS Module Import

این روش مناسبترین روش برای `import` استایل‌های یک کامپوننت است.

```
Cards.jsx
Cards.module.css
```

دو فایل `Cards.jsx` و `Cards.module.css` در کنار هم قرار میدهیم

```css title="Cards.module.css"
.card {
  display: grid;
}
.title {
  font-size: 20px;
  font-weight: 700;
}
/* ... */
```

```jsx {1} title="Cards.jsx"
import styles from "./Cards.module.css";

const Cards = () => {
  return (
    <div className={styles.card}>
      <h2 className={styles.title}>card title</h2>
      <p className={styles.description}>card description ...</p>
    </div>
  );
};
```

در این حالت تداخلی با بقیه‌ی استایل‌ها و کلاس‌های تکراری نداریم

## Inline Styles

مقدار اتریبیوت `style` در jsx یک آبجکت از استایل‌ها میگیرد

```jsx title="Cards.jsx"
const Cards = () =>{
    return(
        <div style={{ display: "grid" }}>
            <h2 style={{ fontSize: "20px", fontWeight: 700 }}>card title</h2>
            <p style={{ ... }}>card description ...</p>
        </div>
    )
}
```

## Styled Components

یک پکیج بنظرم نه چندان خوب! برای استایل دهی هست.

#### نصب

```bash
npm install styled-components
```

برای استفاده از این پکیج باید هر المان از صفحه رو که میخوای مجدد تعریف بکنی و بهش استایل بدی و از اون به بعد از اون المان جدید ساخته شده استفاده بکنی

```jsx titile="Cards.jsx" {1,3-5,16-19}
import styled from "styled-components";

const Div = styled.div`
  display: grid;
`;
const H2 = styled.h2`
  font-size: 20px;
  font-weight: 700;
`;
const P = styled.p`
    ...
`;

const Cards = () => {
  return (
    <Div>
      <H2>card title</H2>
      <P>card description ...</P>
    </Div>
  );
};
```
