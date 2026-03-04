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

بهتر هست که مانند `zod` و برخی فرم ولیدیتورها یک تابع بنویسیم که درصورتی که ارور داشتیم فقط مسیج ارور هارو نشون بده و درغیراینصورت دیتا رو برگردونه

برای مثل اگر ارور داشتیم `success: false` قرار بده و یک آرایه‌ای از ارور ها و درصورت نداشتن ارور `success: true` بشه و دیتا رو برگردونیم. **همچنین از این تابع میتونیم به عنوان میدلور استفاده کنیم**

#### Manage Errors Function

```js title="errorManager.js" {4,16-20}
const { validationResult } = require("express-validator");

const errorManager = (req, res, next) => {
  const errors = validationResult(req);
  let output = {
    success: false,
    errors: {},
  };
  const ers = errors.errors;
  output.success = !Boolean(ers.length);

  if (!output.success) {
    ers.forEach((er) => (output.errors[er.path] = er.msg));

    console.log(output);
    throw {
      status: 400,
      message: "validation failed",
      ...output,
    };
  }
  next();
};
module.exports = errorManager;
```

درواقع این تابع وظیفه‌ی تمیز کردن ارورها رو داره و صرفا کلید و مسیج رو قرار میده برای هر ارور

## Express Validation

### Start

یک پکیج برای ولیدیت کردن فرم ها هست که کاملا با `express-validator` متفاوت است

از `joi` استفاده میکند و یکم کار کردن باهاش رو راحتتر کرده. در آموزش بعدی در رابطه با `joi` حرف میزنیم

:::info
`express-validation` مرحله به مرحله ولیدیت میکند و درصورتی که اولین جایی که ارور دریافت کند اونو برمیگردونه و بقیه‌ی فرم رو رها میکنه
:::

### Implementation

```js title="auth.validator.js"
const { Joi } = require("express-validation");

const loginValidation = {
  body: Joi.object({
    email: Joi.string().email().required(),
    password: Joi.string().min(8).max(24).required(),
  }),
};

module.exports = { loginValidation };
```

```js title="server.js"
const { validate } = require("express-validation");
const { loginValidation } = require("./validator/auth.validator");

app.post("/api/login", validate(loginValidation), (req, res) => {
  res.json(req.body);
});
```

### Custom Message

برای هر ولیدیشن میتونیم یک مسیج دلخواه کاستوم قرار بدیم

```js
const loginValidation = {
  body: Joi.object({
    email: Joi.string().email().message("email is invalid").required(),
    password: Joi.string()
      .min(8)
      .message("password is to short")
      .max(24)
      .message("password is to long")
      .required(),
  }),
};
```

بعد هر آیتم ولیدیشن میتونیم یک تابع `message` قرار بدیم که پیام دلخواهمون باشه

### Query & Params

توی `express-validation` میتونیم query و params رو هم ولیدیت بکنیم.

صرفا باید به ولیدیشنمون علاوه بر body مقادیر param و query رو هم اضافه کرد

```js
const loginValidation = {
  body: Joi.object({
    email: Joi.string().email().required(),
    password: Joi.string().min(8).max(24).required(),
  }),
  params: Joi.object({
    id: Joi.number().min(1).required(),
  }),
  query: Joi.object({
    name: Joi.string().required().min(3),
  }),
};
```

در این حالت باید هم param آیدی داده شود و هم query نام به صورت زیر :

```
/api/login/12?name=ali
```

## Joi

یک کتابخونه برای ولیدیت کردن فرم هست که صرفا `express-validation` یکم کار باهاش رو راحتتر کرده

**Schema** :
در Joi ولیدیشن به فرمت اسیکیما هست و توسط اسکیما ها ولیدیشن انجام میشود

### Implementation

```js title="auth.schema.js"
const Joi = require("joi");

const loginSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string()
    .min(8)
    .message("password must be least 8 chars")
    .max(24)
    .required(),
});

module.exports = { loginSchema };
```

```js title="server.js"
const { loginSchema } = require("./schema/auth.schema");

app.post("/api/login", async (req, res) => {
  await loginSchema.validateAsync(req.body);
  res.json(req.body);
});
```

### Custom Message

دقیقا مانند `express-validation` میتونیم جلوی هر بخش ولیدیشن تابع `message` رو قرار بدیم با پیام دلخواه

```js {5,7}
const loginSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string()
    .min(8)
    .message("password must be least 8 chars")
    .max(24)
    .message("password must be at most 24 chars")
    .required(),
});
```

### Query & Params

برای این حالت باید برای بخش query و params یک درخواست هم یک اسکیما درست بکنیم

یعنی باید هرکدوم از اونارو به صورت جداگانه به اسکیمای مدنظرش پاس بدیم

```js title="auth.schema.js"
const Joi = require("joi");

const loginBodySchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string()
    .min(8)
    .message("password must be least 8 chars")
    .max(24)
    .required(),
});

const loginQuerySchema = Joi.object({
  name: Joi.string().required().min(3),
});

const loginParamsSchema = Joi.object({
  id: Joi.number().min(1).required(),
});

module.exports = { loginBodySchema, loginParamsSchema, loginQuerySchema };
```

```js title="server.js"
const {
  loginBodySchema,
  loginParamsSchema,
  loginQuerySchema,
} = require("./schema/auth.schema");

app.post("/api/login/:id", async (req, res) => {
  await loginBodySchema.validateAsync(req.body);
  await loginQuerySchema.validateAsync(req.query);
  await loginParamsSchema.validateAsync(req.params);
  res.json(req.body);
});
```

در این حالت باید هم param آیدی داده شود و هم query نام به صورت زیر :

```
/api/login/12?name=ali
```
