---
sidebar_position: 4
---

# Form Validation

در این داکیومنت در رابطه با انواع روش‌های اعتبار سنجی فرم‌ها حرف میزنیم

## Express Validator

### Start

بعضی وقتا ما یسری فرم داریم که باید ولیدیت بکنیم و بررسی بکنیم طبق فرمت مدنظر ما هستن یا نه. که یکی از ابزاری که میتونیم ازش استفاده بکنیم `express-validator` هست. که به صورت middleware ازش استفاده میکنیم. یعنی برای مثال در مسیر `/register/` ما چک میکنیم ابتدا `body` که کاربر برای ما فرستاده مطابق schema ما هست یا نه و درصورتی که باشه ادامه میدیم و یوزر رو میسازیم.

```bash
npm install express-validator
```

### Implementation

برای مثال فرض کنید ما یک validator برای فرم ورود داریم. که شامل یک ایمیل و پسورد میشود.

ابتدا باید مشخص کنیم کدوم بخش req رو میخوایم validate بکنیم.

#### body

برای ولیدیت کردن body درخواست استفاده میشه

```js
const { body } = require("express-validator");
```

#### query

برای ولیدیت کردن query های درخواست استفاده میشه

```js
const { query } = require("express-validator");
```

#### params

برای ولیدیت کردت params ها درخواست استفاده میشه

```js
const { param } = require("express-validator");
```

**مثال :**

```js title="auth.validator.js"
const { body } = require("express-validator");

const loginValidator = () => {
  // مقداری که return میشود مورد بررسی قرار میگیرد

  return [
    body("email").isEmail().withMessage("email is invalid"),
    body("password")
      .isLength({ min: 8, max: 24 })
      .withMessage("password length must be between 8 and 24 chars"),
  ];
};

const registerValidator = () => {
  return [
    body("email").isEmail().withMessage("email is invalid"),
    body("password")
      .isLength({ min: 8, max: 24 })
      .withMessage("password length must be between 8 and 24 chars"),
    body("age").custom((value) => {
      if (isNaN(value)) throw new Error("age must be number");
      else if (+value < 18) throw new Error("age must be at least 18");
      else return true;
    }),
  ];
};

module.exports = { loginValidator, registerValidator };
```

زمانی که نیاز به چندتا ولیدیت هست مقدار `return []` برمیگردونیم. اگر صرفا یک آیتم نیاز به ولیدیت داشته باشه میتونیم به صورت `return (body("..."))` داشته باشیم

```js title="server.js" {8,9,14,15}
const { validationResult } = require("express-validator");

const {
  loginValidator,
  registerValidator,
} = require("./validator/auth.validator");

app.post("/api/login", loginValidator(), (req, res) => {
  const errors = validationResult(req);
  if (errors.errors.length) res.status(400).json(errors);
  else res.json({ message: "success" });
});

app.post("/api/register", registerValidator(), (req, res) => {
  const errors = validationResult(req);
  if (errors.errors.length) res.status(400).json(errors);
  else res.json({ message: "success" });
});
```

ولیدیتور مدنظر باید به صورت تابع به عنوان میدلور پاس داده شود

:::info
مقدار میدلور باید همراه پرانتز تابع باشد یعنی به صورت `registerValidator()` درست است زیرا ورودی خاصی ندارد و در ابتدا با استفاده از `validationResult(req)` کال میشود این میدلور
:::

<hr />

#### نکته

ما میتونیم روی params و query ها هم ولیدیت انجام بدیم و صرفا نیاز هست جای

برای مثال برای query داریم :

```js title="auth.validator.js"
const { query } = require("express-validator");

const idValidator = () => {
  return query("id").isMongoId().withMessage("id must be mongodb id format");
};
module.exports = { idValidator };
```

توی این حالت صرفا یک عضو از query رو چک کردیم. میتونیم آرایه‌ای از query هارو هم بررسی کنیم و دقیقا مثل body هست و هیچ فرقی باهم ندارن params, body, query ولیدیشنشون

```js title="server.js"
const { idValidator } = require("./validator/auth.validator");
const { validationResult } = require("express-validator");

app.delete("/api/user", idValidator(), (req, res) => {
  const errors = validationResult(req);
  if (errors.errors.length) res.status(400).json(errors);
  else res.json({ message: "success" });
});
```
