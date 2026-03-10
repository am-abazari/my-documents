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

```js title="swagger.config.js" {17}
const swaggerUI = require("swagger-ui-express");
const swaggerJSDoc = require("swagger-jsdoc");

const SwaggerConfig = (app) => {
  const swaggerDoc = swaggerJSDoc({
    swaggerDefinition: {
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
