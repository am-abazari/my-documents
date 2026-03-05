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

## Router

در بخش‌های قبل در رابطه با Routing صحبت کردیم. در اینجا در رابطه با روش بهتر یعنی استفاده از ماژول `Router` خود express حرف میزنیم

### Start

- در کل هدف مدیریت تمام مسیرها هست برای اینکار ما یک دایرکتوری در مسیر اصلی پروژه به نام `routes` درست میکنیم.

- سپس به ازای هر مسیر یک فایل درست میکنیم. برای مثال ما داریم `/auth/login/` و یا `/auth/register/` یک فایل برای مسیر `auth` درست میکنیم.

- درصورتی که برای مثال مسیر `auth` سنگین شود برای ان یک دایرکتوری درست میکنیم و برای هر مسیر داخل آن یک فایل

- سپس هر مسیر را با استفاده از ماژول `Router` درون express.js تعریف میکنیم

- درنهایت یک فایل مرجع به اسم `routes.js` میسازیم و تمامی مسیر هارا داخل آن قرار میدهیم.

- درنهایت از این فایل `routes.js` به عنوان یک میدلور درون فایل اصلی برنامه یعنی `server.js` استفاده میکنیم که شامل تمام مسیر ها هست

#### مثال :

فرض کن فولدر بندی زیر رو داشته باشیم

![Router Image](/img/router.png)

ظ`routes.js` وظیفه‌ی تجمیع تمام مسیر هارا دارد و از آن درون `server.js` استفاده میکنیم

### Implementation

```js title="routes/auth.router.js"
const { Router } = require("express");

const router = Router();

router.get("/login", (req, res, next) => {
  res.send("auth login get");
});

router.post("/register", (req, res, next) => {
  res.send("auth register post");
});

router.delete("/:id", (req, res, next) => {
  res.send(`auth delete ${req.params.id}`);
});

module.exports = router;
```

```js title="routes/router.js"
const { Router } = require("express");
const authRouter = require("./auth.router");

const router = Router();

router.use("/auth", authRouter);

module.exports = router;
```

```js title="server.js"
const allRoutes = require("./routes/routers");

app.use(allRoutes);
```

در این روش ابتدا اگر ما به مسیر `/auth/login/` درخواست بدیم مقدار `auth login get` کال میشود.

ما میتونیم بقیه route هارو هم به همین طریق تعریف بکنیم و اضافه بکنیمشون

```js title="routes/routers.js"
const { Router } = require("express");
const authRouter = require("./auth.router");
const userRouter = require("./user.router");
const dashboardRouter = require("./dashboard.router");

const router = Router();

router.use("/auth", authRouter);
router.use("/user", userRouter);
router.use("/dashboard", dashboardRouter);

module.exports = router;
```

### Middleware For Router

در اینجا بررسی میکنیم که چطور میتونیم میدلور برای router ها تعریف و استفاده بکنیم.

:::info
درکل توی یک درخواست چه با `router` هندلش بکنیم و چه با خود express فرمت به اینصورت هست که

```js
app.get("url", middleware1, middleware2, ...middlewareN);
app.use("url", middleware1, middleware2, ...middlewareN);

router.get("url", middleware1, middleware2, ...middlewareN);
router.use("url", middleware1, middleware2, ...middlewareN);
```

یعنی تمامی تابع ها بعد url یک میدلور هستند که دونه به دونه توشون `next` صدا زده میشه و باید یکیشون جواب رو برگردونه پس فرقی نمیکنه که از `app.get` استفاده کنیم یا از `router.get` یا از `app.use` یا `router.use`

از هرکدوم که استفاده کنیم میتونیم میدلور داخلش ست بکنیم
:::

<br />

#### میدلور برای تمام API ها

اگر یک میدلور برای تمامی api ها بخوایم باید جایی که `allRoutes` رو تعریف کردیم ست کنیم یعنی :

```js title="server.js"
const allRoutes = require("./routes/routers");

const setTimeMiddleware = (req, res, next) => {
  req.time = Date.now();
  next();
};
app.use(setTimeMiddleware, allRoutes);
```

#### میدلور برای یک دسته از API ها

اگر مثلا بخوایم یک میدلور برای تمام API های زیرمجموعه‌ی `/auth/` اجرا بشه باید اونو به روتر مدنظر اضافه کرد

```js title="routes/routers.js"
const setTimeMiddleware = (req, res, next) => {
  req.time = Date.now();
  next();
};
router.use("/auth", setTimeMiddleware, authRouter);
```

#### میدلور برای یک API خاص

اگر بخوایم فقط یک API یک میدلور رو داشته باشه هم باید به روتر مربوطه اضافه بکنیمش

```js title="routes/auth.router.js"
const setTimeMiddleware = (req, res, next) => {
  req.time = Date.now();
  next();
};
router.get("/login", setTimeMiddleware, (req, res, next) => {
  console.log(req.time);
  res.send("auth login get");
});
```

#### Other Way

یک روش دیگه برای ست کردن میدلور استفاده از `router.use(middleware1,...middlewareN)` هست

```js title="routes/routers.js {5}
const setTimeMiddleware = (req, res, next) => {
  req.time = Date.now();
  next();
};
router.use(setTimeMiddleware, middleware1, ... , middlewareN);

router.use("/auth", authRouter);
router.use("/user", userRouter);
router.use("/dashboard", dashboardRouter);
```

## Controller

بهتر هست ما همواره بخش‌های مستقل از هم رو جدا کنیم. برای مثال middleware از router از controller از model و ... از هم همیشه جدا باشن

در اینصورت خوانایی کد بسیار بالا میرود

![Controller Image](/img/controller.png)

اگر ساختار فولدر بندی بالا رو داشته باشیم :

```js title="controllers/auth.controller.js"
const loginController = (req, res, next) => {
  res.send("auth login get");
};
const registerController = (req, res, next) => {
  res.send("auth register post");
};
const deleteController = (req, res, next) => {
  res.send(`auth delete ${req.params.id}`);
};

module.exports = {
  loginController,
  registerController,
  deleteController,
};
```

```js title="routes/auth.router.js"
const { Router } = require("express");
const {
  loginController,
  deleteController,
  registerController,
} = require("../controllers/auth.controller");

const router = Router();

router.get("/login", loginController);
router.post("/register", registerController);
router.delete("/:id", deleteController);

module.exports = router;
```

## Cookies

### Start

برای استفاده از کوکی ها باید اونارو پارس کنیم و ما از پکیج `cookie-parser` استفاده میکنیم

```bash
npm install cookie-parser
```

و اونو به عنوان میدلور قرار میدهیم :

```js
const cookieParser = require("cookie-parser");

app.use(cookieParser());
```

### Get Cookies

برای دسترسی به کوکی های کاربر از `req.cookies` استفاده میکنیم

```js {2}
app.get("/get-cookies", (req, res, next) => {
  const cookies = req.cookies;
  res.json(cookies);
});
```

### Set Cookies

برای ست کردن کوکی از تابع `res.cookie(key,value)` استفاده میکنیم

```js {2}
app.get("/set-cookies", (req, res, next) => {
  res.cookie("key", "value");
  res.send("successfully added cookie");
});
```

### Remove Cookies

برای حذف کوکی از مرورگر باید از تابع `res.clearCookie` استفاده کرد

```js {2}
app.get("/set-cookies", (req, res, next) => {
  res.clearCookie("key");
  res.send("successfully remoeved cookie");
});
```

### Cookie Options

برخی آپشن‌ها برای کوکی‌ها داریم. برای مثال تا کی وجود داشته باشه و کی از بین بره و ...

#### maxAge

سن کوکی رو نشون میده و میگه چقدر دیگه وجود داشته باشه به میلی‌ثانیه

```js {3}
app.get("/set-cookies", (req, res, next) => {
  res.cookie("key", "value", {
    maxAge: 5000, // 5sec
  });
  res.send("successfully added cookies");
});
```

بعد ۵ ثانیه کوکی از بین میرود

#### expires

تاریخ از بین رفتن کوکی رو میگیره که باید به فرمت `new Date(date)` باشه. یعنی همراه با تاریخ روز ساعت و ...

```js {3}
app.get("/set-cookies", (req, res, next) => {
  res.cookie("key", "value", {
    expires: new Date(Date.now() + 5000), // after 5 sec
  });
  res.send("successfully added cookies");
});
```

بعد ۵ ثانیه کوکی از بین میرود

#### httpOnly

کوکی `httpOnly` به این منظور هست که کوکی مدنظر رو امنیتش رو بالاتر میبره.

مفهومش اینه که تنها کوکی از طریق درخواست http قابل دسترس هست و برای مثال کتابخونه‌هایی که توی فرانت‌اند از اونا استفاده کردیم دسترسی به این کوکی‌ها ندارد

```js {3}
app.get("/set-cookies", (req, res, next) => {
  res.cookie("key", "value", {
    httpOnly: true,
  });
  res.send("successfully added cookies");
});
```

#### signed

در این متند مقدار value کوکی‌ها توسط یک `private key` که به `cookie-parser` میدهیم هش میشوند و در مرورگر کاربر به صورت هش شده ذخیره میشوند

برای دریافت کوکی‌های هش شده به جای `req.cookies` باید از `req.signedCookies` استفاده کرد

```js {1,4,9}
app.use(cookieParser("40!331msdkkdsanf")); // custom private key

app.get("/get-cookies", (req, res, next) => {
  const signedCookies = req.signedCookies;
  res.json(signedCookies);
});

app.get("/set-cookies", (req, res, next) => {
  res.cookie("key", "value", {
    signed: true,
  });
  res.send("successfully added cookies");
});
```

مقدار private-key که توی توی قسمت `cookieParser` قرار میدیم باید یک کلید هش هشده باشه ترجیها

#### secure

این آپشن در کوکی‌ها امنیت کوکی هارو بالاتر میبره و دسترسی بهشون رو محدود تر میکنه

```js {3}
app.get("/set-cookies", (req, res, next) => {
  res.cookie("key", "value", {
    secure: true,
  });
  res.send("successfully added cookies");
});
```

#### sameSite

این آپشن سه حالت داره

- `lax`: حالت پیشفرض کن کوکی هست و به معنی سست(ریلکس) هستش

- `strict`: حالت سختگیرانه هست و با ریدارکت از یک صفحه سایت مثلا به درگاه پرداخت کوکی ها ارسال نمیشن و غیرقابل دسترس هست

- `none`: برای مرورگر های قدیمی هست که کوکی‌ها همواره دردسترس هستن

:::info
همواره sameSite رو با secure استفاده کن. اگر samSite برابر `strict` شد secure رو هم برابر `true` بزار و اگر برابر `lax` یا `none` شد secure رو هم برابر `false` بزار
:::

```js {4}
app.get("/set-cookies", (req, res, next) => {
  res.cookie("key", "value", {
    secure: true, // false
    sameSite: "strict", // lax - none
  });
  res.send("successfully added cookies");
});
```
