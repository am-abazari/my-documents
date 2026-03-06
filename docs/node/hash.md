---
sidebar_position: 7
---

# Hashing

توی این داکیومنت در رابطه با انواع روش‌ها و کتابخونه‌های هش کردن صحبت میکنیم

## Crypto

یک کتابخونه هست که توی core خود node.js وجود داره و نیاز به نصب خاصی نداره

### Start

درکل عملکرد برای هشینگ از قدیم تا به الان خیلی متفاوت شده

در گذشته صرفا پسورد مدنظر هش میشد و هش ها مقایسه میشدن درحالی که این روش میتونه مشکلاتی داشته باشه

روش های جدید به صورت زیر هستند :

1. پسورد دریافت میشود با متغیر `password` ذخیره میشود
2. یک salt رندوم برای هر پسورد ساخته میشود
3. عبارت `password+salt` رو هش میکنیم و عبارت hash رو براش درنظر میگیریم
4. حال با یک فرمتی که مقدار `hash` و `salt` قابل تشخیص باشه اونو توی دیتابیس ذخیره میکنیم
5. برای مثال `#salt#hash#` توی این روش با `split("##)` میتونیم slat و hash رو از هم جدا کنیم

حال وقتی یک پسورد داریم و میخوایم بررسی کنیم که با یک هش برابر هست :

1. عبارت `slat` را از درون hash بیرون می‌اوریم
2. سپس پسورد داده شده را با salt-ای که داریم هش میکنیم
3. درنهایت مقایسه میکنیم هش بدست آمده با هش اولیه یکی هست یا نه

### pbkdf2

یک تابع بسیار کامل و خوب برای هش کردن هست از crypto

:::info
تابع `pbkdf2` به صورت زیر است :

```js
pbkdf2(password, salt, iterations, keylen, protocol);
```

که مقادیر `password` و `salt` مشخص است

`iterations` تعداد تکرار هش را مشخص میکند

`keylen` طول هش بدست آمده را مشخص میکند

`protocol` هم نوع پروتوکل هش کردن هست که معمولا یکی از ۴ آپشن `md5`, `sha1` , `sha256` و یا `sha512` است
:::

مثال :

```js
const crypto = require("crypto");

const createHash = (
  password,
  salt = crypto.randomBytes(16).toString("hex"),
) => {
  const hash = crypto
    .pbkdf2Sync(password, salt, 10, 24, "sha1")
    .toString("hex");
  const hashWithSalt = `#${salt}#${hash}#`;
  return hashWithSalt;
};

const comparePasswordWithHash = (password, hash) => {
  const salt = hash.split("#")[1];
  const hashedPassword = createHash(password, salt);
  return hashedPassword === hash;
};
```

درکل ما یک رشته به فرمت `#salt#password#` داریم که با استفاده از `split` مقدار salt رو بدست میاریم

سپس پسوردی که میخوایم چک کنیم رو با این salt ابتدا hash میکنیم و بررسی میکنیم ۲ هش داده شده برابر هست یا نه

### createHash

یک تابع دیگر برای ساخت هش است که متاسفانه عبارت salt رو ندارد

در کل به فرمت زیر هست :

```js
crypto.createHash(protocol, options).update(password).digest(digest);
```

مثال :

```js
const hash = crypto
  .createHash("sha1", { encoding: "utf-8" })
  .update("password")
  .digest("hex");
```

### createHmac

یک تابع دیگه برای ساخت هش که دقیقا مانند `createHash` هست با این تفاوت که از مقدار salt هم پشتیبانی میکنه

درکل به فرمت زیر هست :

```js
crypto.createHmac(protocol, salt).update(password).digest(digest);
```

مثال :

```js
const salt = crypto.randomBytes(16).toString("hex");
const hash = crypto.createHmac("sha1", salt).update("password").digest("hex");
```

## Bcrypt

توی این کتابخونه دردسر های ساخت salt و اضافه کردنش به هش نهایی و سپس split کردنش رو نداریم

درواقع توی این کتابخونه خودش به طور اتومات مفدار salt رو توی هش نهایی نگه میداره و تابع برای مقایسه یک پسورد با یک هش داره

### Start

```bash
npm install bcrypt
```

### Implementation

این کتابخونه خودش مقادیر salt رو هندل میکنه و نیاز به انجام کار خاصی نیست. درواقع مقدار slat رو توی هش نهایی یجایی جا داده که هنگام مقایسه و `compare` خودش اونو استخراج میکنه از هشی که دادی

```js
const bcrypt = require("bcrypt");

const createHash = (password) => {
  const salt = bcrypt.genSaltSync(10);
  const hash = bcrypt.hashSync(password, salt);
  return hash;
};

const comparePasswordWithHash = (password, hash) => {
  return bcrypt.compareSync(password, hash);
};
```

:::info
تابع `genSaltSync` یک عدد دریافت میکنه که تعداد دور هش کردن رو میگیره
مقدار دیفالتش 10 هست و نیاز نیست خیلی بشه.

مقادیر بالاتر از 13 بیشتر از ۱ ثانیه طول میکشه که افتضاح هست !
:::

## sha1

یه پکیج ساده که صرفا یک تابع هست و با پروتوکول `sha1` هش میکنه

```bash
 npm install sha1
```

```js
const sha1 = require("sha1");
const hash = sha1(password);
```

## md5

یه پکیج ساده که صرفا یک تابع هست و با پروتوکول `md5` هش میکنه

```bash
 npm install md5
```

```js
const md5 = require("md5");
const hash = md5(password);
```
