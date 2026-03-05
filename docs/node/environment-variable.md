---
sidebar_position: 6
---

# Environment Variables

درباره‌ی فایل `.env` حرف میزنیم و کنترل محیط‌های متفاوت در آن

## Start

فایلی با نام `.env` در مسیر اصلی پروژه در بالاترین سطح میسازیم

از پکیج `dotenv` برای پارس کردن مقادیر `env` استفاده میکنیم

```bash
npm install dotenv
```

## Config

برای پارس دیتا داخل `.env` باید از پکیج `dotenv` استفاده کرد و آنرا کانفیگ کرد

در بالاترین فایل کانفیگ زیر را اضافه میکنیم

```js title="server.js"
const dotenv = require("dotenv");

dotenv.config();
```

گاهی وقتا ممکنه در لاگ سرور بعضی ارور ها یا وارنینگ‌ها یا پیشنهادهایی بده که میتوینم با استفاده از فلگ `quiet` اونارو غیر فعال کنیم :

```js
dotenv.config({ quiet: true });
```

## Use

حال میتوانیم در فایل `.env` مقادیر دلخواه تعریف کنیم و به آن با استفاده از `proccess.env` دسترسی به صورت عادی داشته باشیم

```dotenv title=".env"
API_URL=api.google.com
```

حال درون هر بخش برنامه میتوان از آن استفاده کرد

```js
const API = process.env.API_URL;
```

## Multiple Environment

گاهی وقتا متغیر‌های مرتبط با محیط توسعه و محیط پروداکشن متفاوت هستن. برای مثال لینک api متفاوت میتونه باشه یا لینک دیتابیس

ما برای هرکدوم یک فایل `env` میسازیم و در فایل اصلی `.env` نوع محیط توسعه رو انتخاب میکنیم و سپس با توجه به آن کتابخونه‌ی `dotenv` رو طبق اون کانفیگ میکنیم

برای مثال اگر ما ۲ نوع محیط توسعه داشته باشیم یکی `development` و یکی `production` برای هرکدام یک فایل میسازیم به ترتیب : `.development.env` و `.production.env`

و توی فایل `.env` مشخص میکنیم که کدام محیط هست و سپس فایل کانفیگ مرتبط رو به `dotenv` میدیم

```dotenv title=".development.env"
PORT=8000
API_URL=dev.api.example.com/v1
DB_URL=dev.db.example.com
```

```dotenv title=".production.env"
PORT=5031
API_URL=api.example.com/v3
DB_URL=db.example.com
```

```dotenv title=".env"
NODE_ENV=production
```

```js title="server.js"
dotenv.config();

const NODE_ENV = process.env.NODE_ENV;

if (NODE_ENV === "production") dotenv.config({ path: ".production.env" });
else if (NODE_ENV === "development")
  dotenv.config({ path: ".development.env" });

// ----- OR -------

dotenv.config({ path: `.${NODE_ENV}.env` }); // shorter way
```
