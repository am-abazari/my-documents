---
sidebar_position: 8
---

# Swagger UI

ابزاری برای نمایش داکیومنت API ها هست که بدون نیاز به نرم‌افزار های جانبی مثل postman میتونیم api testing انجام بدیم

## Express Swagger

این بخش مربوط به پیاده‌سازی swagger درون express.js هست

### Start

برای شروع کار ۲ پکیج زیر را نیاز داریم:

```bash
npm install swagger-ui-express swagger-jsdoc
```

### Config Swagger

کانفیگ اولیه‌ی swagger رو توی فایلی به اسم `swagger.config.js` در مسیر کانفیگ‌های پروژه قرار میدیم

```js title="swagger.config.js" {20,14}
const swaggerUI = require("swagger-ui-express");
const swaggerJSDoc = require("swagger-jsdoc");

const SwaggerConfig = (app) => {
  const swaggerDoc = swaggerJSDoc({
    swaggerDefinition: {
      openapi: "3.0.0",
      info: {
        title: "your title",
        description: "your description",
        version: "project version",
      },
    },
    apis: [],
  });
  const swagger = swaggerUI.setup(swaggerDoc, {
    customSiteTitle: "title",
  });

  app.use("/swagger", swaggerUI.serve, swagger);
};

module.exports = { SwaggerConfig };
```

سپس این کانفیگ رو در فایل اصلی پروژه باید اجرا کنیم و زینپس داکیومنت‌های swagger در مسیر `/swagger` قابل مشاهده هست

```js title="server.js"
const { SwaggerConfig } = require("@configs/swagger.config");

const app = express();

SwaggerConfig(app);
```

برای مثال برای کانفیگ اولیه‌ی یک داکیومنت API برای بکند من به صورت زیر عمل میکنم :

```js title="example"
const swaggerUI = require("swagger-ui-express");
const swaggerJSDoc = require("swagger-jsdoc");

const SwaggerConfig = (app) => {
  const swaggerDoc = swaggerJSDoc({
    swaggerDefinition: {
      openapi: "3.0.0",
      info: {
        title: "Divar Backend",
        description: "Divar Backend Developed by Amirhossein Abazari",
        version: process.env.VERSION,
      },
    },
    apis: [],
  });
  const swagger = swaggerUI.setup(swaggerDoc, {
    customSiteTitle: "Divar Backend API",
  });

  app.use("/swagger", swaggerUI.serve, swagger);
};

module.exports = { SwaggerConfig };
```

### API's

همانطور که بالاتر اشاره شد مقدار `api:[]` آرایه‌ای هست که داکیومنت مربوط به api های مارو مشخص میکنه که درکجا قرار دارن

برای مثال اگر ما داکیومنت تمام سواگر api هامون داخل یک دایرکتوری به اسم documents نگه میداریم در اینجا باید به اون اشاره بکنیم

برای مثال من تمام داکیومنت‌هارا در مسیر `/src/documents/` نگهداری میکنم :

```js title="example" {1,11}
const documents = path.join(process.cwd(), "src", "documents", "*.swagger.js");
const swaggerDoc = swaggerJSDoc({
  swaggerDefinition: {
    openapi: "3.0.0",
    info: {
      title: "",
      description: "",
      version: "",
    },
  },
  apis: [documents],
});
```

این یعنی تمام فایل‌هایی را که فرمت `name.swagger.js` دارن رو در مسیر `/src/documents/` درنظر بگیر.

### Write Document

حالا میخوایم یک داکیومنت سواگر بنویسیم پس از کانفیگ کردنش

یک فایل به فرمت `name.swagger.js` در مسیری که داکیومنت‌هارو مشخص کردیم میسازیم

حالا توی این داکیومنت یک tag از end-point ها داریم. درواقع نقش folder توی اپلیکیشن‌های postman داره. برای این tag ما توضیحات اولیه‌اش رو به صورت زیر قرار میدیم

```js
/**
 * @swagger
 * tags:
 *  name: Collection Name
 *  description: Collection Description
 */
```

حال باید برای هر بخش یک schema تعریف کنیم. برای مثال اگر ما برای یک فرم شامل یوزر نیم و پسورد میفرستیم باید برای این فرم یک اسکیما تعریف کنیم که فیلد یوزرنیم خودش یک اسکیما و فیلد پسورد هم خودش یک اسکیما داره و این فرم کلش یک اسکیما واحد میشه

```js
/**
 * @swagger
 * components:
 *  schemas:
 *    schema-name:
 *      type: object
 *      required:
 *        - field-1
 *        - field-2
 *      properties:
 *        field-1:
 *          type: string
 *        field-2:
 *          type: number
 *        field-3:
 *          type: string
 */
```

سپس باید end-point های api رو برای این کالکشن مشخص کنیم:

```js {4,7,12,15}
/**
 * @swagger
 * /end-point-url:
 *  methoad:
 *    summary: end-point summary
 *    tags:
 *      - Tags name
 *    requestBody:
 *      content:
 *        application/x-www-form-urlencoded:
 *          schema:
 *            $ref: '#/components/schemas/schema-name'
 *        application/json:
 *          schema:
 *            $ref: '#/components/schemas/schema-name-2'
 *    responses:
 *      200:
 *        description: success
 *      400:
 *        description: bad input
 *
 */
```

- methoad : باید نوع متد مدنظر ما باشه برای مثال post - put - get , delete , ...

- Tag name : اشاره میکنه که این اندپوینت مربوط به کدوم tag هست و باید بیاد زر مجموعه‌ی کدومشون باشه

- درواقع توی مثال بالا ما یک اندپوینت به مسیر `/end-point-url` تعریف کردیم. گفتیم نوع دیتایی که میفرستیم میتونه به ۲ حالت `application/x-www-form-urlencoded` یا `application/json` باشه و از طرفی اسکیما دیتایی که ارسال میکنیم رو از اسکیما هایی که قبلا تعریف کردیم با استفاده از `$ref` رفرنس میدیم

برای مثال ما یک داکیومنت برای Auth بکند دیوار داریم و به صورت زیر داکیومنتش رو مینویسیم:

```js title="auth.swagger.js"
/**
 * @swagger
 * tags:
 *  name: Auth
 *  description: Authentication API Calls
 */

/**
 * @swagger
 * components:
 *  schemas:
 *    SendOTP:
 *      type: object
 *      required:
 *        - mobile
 *      properties:
 *        mobile:
 *          type: string
 *    CheckOTP:
 *      type: object
 *      required:
 *        - mobile
 *        - code
 *      properties:
 *        mobile:
 *          type: string
 *        code:
 *          type: number
 *
 */

/**
 * @swagger
 * /auth/send-otp:
 *  post:
 *    summary: Sending OTP code for login
 *    tags:
 *      - Auth
 *    requestBody:
 *      content:
 *        application/x-www-form-urlencoded:
 *          schema:
 *            $ref: '#/components/schemas/SendOTP'
 *        application/json:
 *          schema:
 *            $ref: '#/components/schemas/SendOTP'
 *    responses:
 *      200:
 *        description: success
 */

/**
 * @swagger
 * /auth/check-otp:
 *  post:
 *    summary: Checking is OTP correct or not
 *    tags:
 *      - Auth
 *    requestBody:
 *      content:
 *        application/x-www-form-urlencoded:
 *          schema:
 *            $ref: '#/components/schemas/CheckOTP'
 *        application/json:
 *          schema:
 *            $ref: '#/components/schemas/CheckOTP'
 *    responses:
 *      200:
 *        description: success
 */
```

توی نمونه‌ی بالا ۲ اند پوینت که جفتشون از نوع post هستن یکی با مسیر `/auth/send-otp/` و دیگری با مسیر `/auth/check-otp/` تعریف شدن

که اولی فقط شماره‌ی موبایل و دومی شماره موبایل به همراه کد otp رو تعریف میکنه

از طرفی ما ۲ اسکیما ساختیم که یکی فقط شماره موبایل داره و دیگری شماره موبایل به همراه کد

نتیجه به شکل زیر هست :
![Swagger Image](/img/swagger.png)
