---
sidebar_position: 3
---

# express.js

داکیومنت مرتبط با فریم‌وورک express.js در اینجا آورده شده

## Start Server

برای شروع سرور یک `instance` از express.js میسازیم و کل پروژه رو روی اون بنا میکنیم

```js
const express = require("express");

const PORT = 8000;

const app = express();

app.listen(PORT, () => console.log(`Server listening on port ${PORT}`));
```

در این حالت سرور بالا می‌آید و حال باید route handling انجام دهیم زیرا به طور پیشفرض express فرض ‌می‌کند که مسیری وجود ندارد

## Route Handling

برای route handling در express باید از اینستنس app استفاده کرد به صورتی که به فرمت زیر است :

```js
app.methoad("url", (req, res) => {
  // ....
  res.status(...).send(...);
});
```

به صورتی که methoad یکی از متد های معتبر http مثل `get` , `post` , `put` , ... است

:::info
تابع `send` به طور خودکار نوع `Content-Type` دیتای ارسالی رو تشخیص میدهد

اما معمولا برای ارسال دیتای `json` از `app.json` استفاده میشود
:::

### Routes

برای مثال چند نمونه از route handling هارو داریم

#### GET

```js
app.get("/users", (req, res) => {
  res.json([
    {
      id: 1,
      name: "Amirhossein",
      username: "am_abazari",
    },
    {
      id: 2,
      name: "Ali",
      username: "alim",
    },
  ]);
});
```

#### POST

```js
app.post("/users", (req, res) => {
  res.status(201).json({
    message: "user created successfully",
  });
});
```

#### PUT

```js
app.put("/users", (req, res) => {
  res.json({
    message: "users updated successfully",
  });
});
```

#### DELTE

```js
app.delete("/users", (req, res) => {
  res.json({
    message: "users delete successfully",
  });
});
```

#### regex

ما توی مسیردهی میتونیم از regex هم استفاده بکنیم
برای مثال

```js
app.get(/[a-z0-9\.\_]@[a-z]{2,6}\.[a-z]{2,10}/, (req, res) => {
  res.send(req.url);
});
```

:::info
اگر status به صورت دیفالت ثبت نشود مقدار ۲۰۰ درنظر گرفته میشود
:::

این یک فرمت email هست. یعنی باید متد `get` به مسیری به شکل زیر باشه

`http://localhost:8000/amirhoss@gmail.com`

### Params

برای مثال اگر مسیر `/api/products/123/` مقدار `123` متغیر هست پس باید از لاجیک `params` استفاده کرد

باید در مسیردهی از عبارت `:name` استفاده کرد و مقدار آنرا میتوان از `const {name} = req.params` دریافت کرد

```js
app.get("/api/products/:id", (req, res) => {
  const { id } = req.params;

  if (!Number(id))
    res.status(404).json({ status: 404, message: "product not found" });

  res.json({
    id: id,
    message: `product ${id} returned`,
  });
});
```

میتونیم چند dynamic route داشته باشیم و مقداری رو از داخل params بگیریم

```js
app.get("/api/products/:id/lists/:listID", (req, res) => {
  const { id, listID } = req.params;

  if (!Number(id))
    res.status(404).json({ status: 404, message: "product not found" });

  res.json({
    id: id,
    message: `product ${id} returned`,
    listID: listID,
  });
});
```

### Query

گاهی وقتا نیاز هست به یک مسیر query پاس بدیم

یعنی برای مثال آدرسی به فرمت زیر داشته باشیم : `api/products/delete?id=12`
برای گرفتن مقدار `id` چون متغیر هست باید از `req.query` استفاده کرد

```js
app.get("/api/products", (req, res) => {
  const queries = req.query;

  res.json({
    message: "success",
    queries: queries,
  });
});
```

حال اگر برای مثال وارد مسیر ` api/products?age=salam&id=12` مقدار خروجی به صورت زیر خواهد بود :

```json
{
  "message": "success",
  "queries": {
    "age": "salam",
    "id": "12"
  }
}
```

## Middlewares

### تعریف

میدلور ماژولی هست که قبل از اینکه درخواست کاربر رو به متد مدنظر و یا میدلور بعدی پاس بدهد ابتدا وارد آن میشود

`request -> middleware1 -> middleware2 -> ... -> methoad handler`

یعنی ابتدا درخواست دریافت میشود. تک تک وارد تمام میدلور ها میشود و درصورتی که تمام میدلور هارا پاس کند وارد متد مدنظر با url متناظرش میشود اما ممکن است از یکی از این میدلور ها با موفقیت عبور نکند و درجا همانجا به کاربر پاسخ داده شود.

### استفاده

برای مثال فرض کن ما قصد داریم کاربرانی که اهل اسرائیل هستن رو راه ندیم. در این حالت یک میدلور ست میکنیم که ip کاربر رو چک بکنه و درصورت تایید به میدلور بعدی بره و دغیراینصورت ارور ۴۰۳ بده

### پیاده سازی

ما با استفاده از تابع `use` میتونیم یک میدلور تعریف بکنیم

میدلور ها به ترتیب تعریف طی میشوند و با صدا زدن تابع `next` به میدلور بعدی میروند

```js
app.use((req, res, next) => {
  // ... checkes or converts
  next();
});
```

در یک میدلور باید یا `next` صدا زده شود که به میدلور بعدی برود یا `res` صدا زده شود که پاسخ کاربر مستقیم داده میشود

بدیهی است که بعد از صدا زدن تابع `next` عبارت های بعد آن خوانده نمیشود و مستقیم به میدلور بعدی میرود

### افزودن مقادیری به درخواست

ما با استفاده از میدلور ها میتونیم مقادیر دلخواهی به درخواست اضافه کنیم یا درخواست رو بررسی کنیم که valid هست یا نه

```js
app.use((req, res, next) => {
  const time = Date.now();
  req.time = time;
  next();
});
```

حال توی هر درخواست با استفاده از `req.time` میتونیم به مقدار مدنظر زمان دسترسی داشته باشیم

### body parser

برای دریافت دیتای ارسالی کاربر باید از میدلوری به اسم body parser استفاده کرد. که به صورت پیشفرض در express وجود دارد

```js
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
```

این دو خط کد رو به عنوان اولین میدلرور ها قرار میدهیم که هر درخواست کاربر body اون رو پارس بکنه و قابل مشاهده باشه

### morgan

یک پکیجی هست که به عنوان میدلور استفاده میشود و نشان میدهد هر درخواست از کجا اومده و به کجا میره و جزییاتش چیه

```js
app.use(morgan("dev"));
```

انواع مختلف داره مانند `dev`, `tiny`, ... و حتی قابل کاستوم کردن هست

### custom middleware

بعضی وقتا میخوایم بعضی میدلور ها فقط برای بعضی از route ها اعمال بشه که در این حالت باید مستقیما میدلور مدنظر رو به route مدنظر پاس بدیم به فرمت زیر :

```js
app.get("url", middleware1, middleware2, ..., middlewareN, (req, res) => {
  // ....
});
```

برای مثال داریم :

```js {3,10,7}
const setTime = (req, res, next) => {
  const time = Date.now();
  req.time = time;
  next();
};

app.get("/api/products", setTime, (req, res) => {
  res.json({
    message: "success",
    time: req.time,
  });
});
```

درکل عبارت `app.methoad("url",...")` به جای `...` میتونه هرتعدادی تابع دریافت کنه که آخرین تابع باید مقدار `res` رو داشته باشه.

### Not Found

اگر api وجود نداشته باشد بهتر هست که به صورت دستی ۴۰۴ بدیم و از دیفالت اکسپرس استفاده نکنیم.

برای اینکار باید در انتهای تعریف تمام route ها یک میدلور به صورت زیر تعریف کنیم :

```js
app.use((req, res) => {
  res.status(404).json({
    status: res.statusCode,
    message: "api not found",
  });
});
```

### Error Handling

درصورتی که در یک بخش اروری رخ دهد به صورت پیشفرض اکسپرس ارور را نمایش میدهد اما ظاهر بد و غیرخوانا دارد که برای اینکار ما آنرا به صورت دستی تغییر میدهیم

در آخر آخر آخر پس از میدلور 404 و not found یک میدلور به صورت زیر اضافه میکنیم

میتونیم از try catch هم برای ارور هندلینگ بهتر استفاده کنیم درکنارش

```js
app.use((err, req, res, next) => {
  res.json({
    status: err.statusCode || 500,
    message: err.message || "Internal server error",
  });
});
```

یعنی تعریف تمام route ها و middleware ها باید به صورت زیر باشد :

```
middlware 1
middlware 2
...
middleware N

route 1
route 2
...
route M

middleware 404
middleware error
```

درکل ابتدا تمام میدلور ها تعریف میشوند سپس تمام مسیر ها و درنهایت صفحات ۴۰۴ و سپس ارور ها هندل میشوند

**مثال :**

```js
// middlewares
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(morgan("dev"));

// routes
app.get("/api/products", (req, res) => {
  res.status(200).json({
    message: "success",
    time: Date.now(),
  });
});
app.post("/api/products", (req, res) => {
  res.status(200).json({
    message: "success",
  });
});

// 404 not found
app.use((req, res) => {
  res.status(404).json({
    status: res.statusCode,
    message: "api not found",
  });
});

// error handli ng
app.use((err, req, res, next) => {
  res.json({
    status: err.statusCode || 500,
    message: err.message || "Internal server error",
  });
});
```
