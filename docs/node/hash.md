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

### randomInt

یک تابع برای جنیرت کردن یک عدد رندوم بین دو بازه هست
و بازه‌ی ما به صورت `(min,max]` هست یعنی میتونه مقدار min رو اخذ کنه ولی max رو نه

```js
crypto.randomInt(min, max); // [min, max)
```

برای مثال برای ساخت یک کد OTP به طول length کد زیر رو داریم :

```js
const crypto = require("crypto");

const createOTP = (length) => {
  return crypto.randomInt(Math.pow(10, length - 1), Math.pow(10, length));
};
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

## JWT

مخفف json web token هست که در سایت [jwt.io](https://jwt.io) دردسترس است و روشی برای هش کردن توکن ها هست که شامل برخی اطلاعات داخلش میشود

از ۳ بخش تشکیل شده توکن که توسط `.` از هم جدا میشوند

1. بخش اول توکن پروتکل هش را نشان میدهد
2. بخش دوم اطلاعاتی که درباره‌ی کاربر ما خودمان ست کردیم را نشان میدهد
3. بخش سوم کلید هش را نشان میدهد

بخش اول و دوم قابل decode هستند و نیاز به کار خاصی ندارد ولی بخش سوم تنها با کلید خصوصی قابل دسترس هست

### Start

```bash
npm install jsonwebtoken
```

برای مثال اگر توکن زیر رو داشته باشیم

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6InNhbGFtIiwiaWF0IjoxNzcyNzkxNjU1fQ.H3oPA8v9125Gf9FL2sO9eYue2MobMNwmJ6YF1uq8AtI

از ۳ قسمت تشکیل شده که با `.` قابل جدا شدن هستن

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9` : مربوط به پروتکل هش هست و قابل دیکود کردن هست

`eyJpZCI6InNhbGFtIiwiaWF0IjoxNzcyNzkxNjU1fQ` : مربوط به دیتایی هست که ما بهش دادیم و قابل دیکود کردن هست. درنتیجه دیتایی مانند آیدی کاربر، ایمیل کاربر و یا ... بهش بده که آنچنان اهمیتی نداشته باشه. مثلا پسورد نباید داده بشه بهش

`H3oPA8v9125Gf9FL2sO9eYue2MobMNwmJ6YF1uq8AtI` : این بخش هش شده‌ی private key ما هست که غیر قابل دیکود هست

### Create Token

برای ساخت توکن باید از تابع `sign` پکیج jwt استفاده کرد به فرمت زیر

```js
jwt.sign(data, secret, options);
```

- بخش data همان داده‌هایی هست که ما برای هش کردن بهش میدیم و قابل decode کردن هست. برای مثال id کاربر یا ایمیل و ... میدیم معمولا
- بخش secret همون private-key هست که برای هش کردن بخش سوم استفاده میشود
- بخش options هم آپشن هایی مثل `algorithm` , `expiresIn`, `encoading` و ... رو داره

#### مثال :

```js
const jwt = require("jsonwebtoken");

const secret = "d2268bf968acbdb9b99fff5856227341";
const token = jwt.sign({ id: "user_id", email: "user_email" }, secret, {
  expiresIn: "1d", // 1000 = 1000ms, 1s = 1sec, 1d = 1day, 1w = 1week, ...
  algorithm: "HS512",
});
```

الگوریتم‌های متفاوتی برای هش کردن وجود داره ولی ما اینجا از `HS512` استفاده کردیم. برای مثال اگر از `RS512` استفاده کنیم باید عبارت `secret` ما یک ssh-key باشه

خروجی کد بالا برابر زیر هست :

eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJpZCI6InVzZXJfaWQiLCJlbWFpbCI6InVzZXJfZW1haWwiLCJpYXQiOjE3NzI3OTIyMzUsImV4cCI6MTc3Mjg3ODYzNX0.Q-jPhWK6jJYWgIaGj3HZyKmhwVLOjQAyiUTkcpnKhI2b4KSArxORgJ-cKiNyX-qhfUbvHncqSL7MFjCOeaFXKQ

این توکن رو درصورتی که توی سایت https://jwt.io اگر ببینیم میتونیم مشاهده کنیم که بخش data قابل استخراج هست

### Decode

ما میتونیم بخش‌های ۱(الگوریتم) و ۲(دیتایی که دادیم) رو decode و مشاهده کنیم بدون داشتن private-key

```js
jwt.decode(token);
```

#### مثال :

```js
const decoded = jwt.decode(
  "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJpZCI6InVzZXJfaWQiLCJlbWFpbCI6InVzZXJfZW1haWwiLCJpYXQiOjE3NzI3OTIyMzUsImV4cCI6MTc3Mjg3ODYzNX0.Q-jPhWK6jJYWgIaGj3HZyKmhwVLOjQAyiUTkcpnKhI2b4KSArxORgJ-cKiNyX-qhfUbvHncqSL7MFjCOeaFXKQ",
);
```

خروجی کد بالا برابر زیر است :

```json title="output"
{
  "id": "user_id",
  "email": "user_email",
  "iat": 1772792235,
  "exp": 1772878635
}
```

همانطور که مشاهده میشود بدون داشتن private-key ما تونیستیم به data دسترسی داشته باشیم

### Verify

فرق verify با decode این هست که با ما یک توکن رو با داشتن private-key یا همون secret تایید میکنیم. یعنی بررسی میکنیم که یک secret برای یک توکن هست یا نه

```js
jwt.verify(token, secret);
```

درصورت درست بودن secret مقادیر decode شده دقیقا مانند خروجی تابع decode برگردانده میشود و درصورت درست نبودن یک ارور پرتاب(throw) میشود

#### مثال

```js
const verified = jwt.verify(
  "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJpZCI6InVzZXJfaWQiLCJlbWFpbCI6InVzZXJfZW1haWwiLCJpYXQiOjE3NzI3OTIyMzUsImV4cCI6MTc3Mjg3ODYzNX0.Q-jPhWK6jJYWgIaGj3HZyKmhwVLOjQAyiUTkcpnKhI2b4KSArxORgJ-cKiNyX-qhfUbvHncqSL7MFjCOeaFXKQ",
  "d2268bf968acbdb9b99fff5856227341",
);
```

خروجی کد بالا چون secret صحیح است به صورت زیر است :

```json title="output"
{
  "id": "user_id",
  "email": "user_email",
  "iat": 1772792235,
  "exp": 1772878635
}
```

درصورت نادرست بودن secret ارور پرتاب میشود

:::warning
نکته‌ی خیلی مهم : ذخیره کردن توکن در دیتابیس کاملا اشتباه هست. صرفا باید وریفای کرد

یعنی اگر برای یک یوزر توکن ساختی و اونو بهش دادی دیگه نیاز نیست توی دیتابیس ذخیره‌اش بکنی.

صرف وریفای کردن توکن با استفاده از `jwt.verify` کافیه چون دسترسی به Secret وجود نداره
:::
