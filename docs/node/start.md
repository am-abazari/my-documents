---
sidebar_position: 1
---

# شروع

## -----Insert last notes on Above ----

## Absolute Alias

برای مسیردهی به فایل‌ها، ماژول‌ها، مدل‌ها و ... ما نمیتونیم هربار چندین بار `../../` بزنیم چونکه اگر یکبار صرفا یک فایل رو جاشو عوض کنیم باید در تمام فایل‌ها مسیرش رو تغییر بدیم

برای همین ما از مسیردهی absolute استفاده میکنیم و معمولا با `@` شروعش میکنیم

### Module Alias

یک پکیج هست که میتونیم ازش برای مسیردهی استفاده کنیم

```bash
npm install module-alias
```

سپس توی فایل `package.json` مسیر هایی رو که میخوایم تعریف میکنیم

```json title="package.json"
{
  "_moduleAliases": {
    "@": "./src",
    "@controller": "./src/controller",
    "@model": "./src/model",
    "@middleware": "./src/middleware",
    "@router": "./src/router",
    "@util": "./src/util",
    "@config": "./src/util/config",
    "@function": "./src/util/function"
  }
}
```

سپس در بالاترین سطح برنامه `module-alias/register` رو ایمپورت میکنیم. دقت کن که یکبار در بالاترین سطح برنامه کافی هست ایمپورتش و **نیازی نیست** توی هر فایل مجدد ایمپورت کنیم برای استفاده

```js title="server.js" {1}
require("module-alias/register");
const express = require("express");
// ...
```

سپس میتونیم از module-alias توی فایل‌های مختلف برنامه استفاده بکنیم و نیازی به ایمپورت مجدد چیزی نیست.

برای مثال :

```js title="example"
// router
const { RegisterController } = require("@controller/auth/register.controller");
const { LoginController } = require("@controller/auth/login.controller");
const { LogoutController } = require("@controller/auth/logout.controller");

// model
const { UserModel } = require("@model/user.model");

// function
const { verifyTokne } = require("@function/token");
```

سپس به این نحو استفاده میکنیم ازش
