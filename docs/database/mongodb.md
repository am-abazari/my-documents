---
sidebar_position: 1
---

# MongoDB

یک دیتابیس nosql هست که درصورتی که relation داخلش زیاد نباشه سرعت بسیار خوبی داره و درصورت زیاد شدن relation ها سرعتش به طور محسوسی کم میشه

**توی این داکیومنت ما پیاده‌سازی MongoDB درون Node.js رو میگیم درحالی که در بقیه‌ی زبان ها هم قابل استفاده و پیاده‌سازی هست**

از طرفی دیتابیس MongoDB کدها و کوئری‌هاش خیلی شبیه زبان Javascript هست

## Start

ما از ODM در Node.js برای راحتی کار استفاده میکنیم که بدونیم مدل سازی کنیم و مدل بسازیم

### Install

ما از Mongoose به عنوان ODM استفاده میکنی

```bash
npm install mongoose
```

### Definitions

ما اینجا برخی از تعاریف دیتابیس‌های nosql رو آوردیم

- **Collection** : همون معنی table در دیتابیس sql-ای رو میده

- **Document** : معنی یک سطر داخل دیتابیس sql-ای رو میده که درواقع یک عضو از یک Collection هست

- **ORM** : ابزاری برای اتصال به دیتابیس‌های sql ای هست که نیاز نباشه کد‌های مختلف برای دیتابیس‌های مختلف زد و یک کد روی انواع دیتابیس‌های sql ای کار میکنه

- **ODM** : معادل ORM برای دیتابیس‌های nosql هست که D به خاطر Document در دیتابیس nosql هست

## Connect to Database

درصورتی که دیتابیسی که اسمشو میزنیم وجود نداشته باشه اونو میسازه

ما برای کانفیگ کانکشن به دیتابیس در دایرکتوری `configs` یک فایل به اسم `mongodb.config.js` میسازیم

```js title="configs/mongodb.config.js" {9}
const { default: mongoose } = require("mongoose");

const DB_URL = "mongodb://localhost:27017";
const DB_NAME = "mongodb-learning";
const URL = `${DB_URL}/${DB_NAME}`;

const ConnectDB = async () => {
  try {
    await mongoose.connect(URL);
    console.log("Connected to DB Successfully");
  } catch (err) {
    console.log(err.message);
  }
};

module.exports = ConnectDB;
```

سپس از این کانفیگ درون `server.js` یا فایل اصلی پروژه برای اتصال به دیتابیس استفاده میکنیم

```js title="server.js" {1,5}
const ConnectDB = require("./configs/mongodb.config");

app.use(express.json());
app.use(express.urlencoded({ extended: true }));
ConnectDB();

// Routes, Others ...
```

## Collection

### Create Collection

model همون معادل table در دیتابیس‌های sql ای هست که توسط اسکیما ساخته میشود

در این قسمت ما یک collection را schema بهش اختصاص میدیم و میسازیمش

ما برای تمام model ها یک دایرکتوری با اسم `models` میسازیم و تمام مدل‌ها را درون آن قرار میدهیم. برای مثال برای مدل کاربران یک فایل با اسم `users.mode.js` میسازیم و داخل آن schema مرتبط به یوزرها رو تعریف میکنیم

```js title="models/user.model.js"
const { Schema, model } = require("mongoose");

const UserSchema = new Schema(
  {
    username: { type: String, required: true, unique: true },
    password: { type: String, required: true },
  },
  {
    timestamps: true,
  },
);
const UserModel = model("user", UserSchema);
module.exports = UserModel;
```

برخی فلگ‌ها و محدودیت‌ها توی فیلدها میتونیم اعمال کنیم مثل `required` و .... برخیشون رو اینجا اوردیم

```json lines
{
  "type" : String, // Boolean, Number, Schema, [String], [Number], ...
  "required": true,
  "unique": true,
  "trim": true,
  "minLength": 10,
  "maxLength": 12,
  "default": "default value"
}
```

### Insert One Document

این متد معادل Insert درون دیتابیس‌های sql ای هست. یعنی یک داکیومنت به collection اضافه میکنیم

میتونیم با ۲ تا تابع `create` و یا `insertOne` این کارو انجام بدیم

#### create :

```js
await ModelName.create({
  // fields ...
});
```

#### insertOne :

```js
await ModelName.insertOne({
  // fields ...
});
```

برای مثال برای درج یک یوزر درون user collection ما به صورت زیر عمل میکنیم :

```js title="example"
const result = await UserModel.create({
  username: "amirhossein",
  password: "iran12Sa@!3",
});
```

همچنین با `insertOne` هم میشه انجام داد

```js title="example"
const result = await UserModel.insertOne({
  username: "amirhossein",
  password: "iran12Sa@!3",
});
```

مقدار `result` برابر نتیجه هست که شامل فیلد ساخته شده هست

```json title="result"
{
  "username": "amir",
  "password": "ali",
  "_id": "69ad61b013e6bde575e146f0",
  "createdAt": "2026-03-08T11:46:56.960Z",
  "updatedAt": "2026-03-08T11:46:56.960Z",
  "__v": 0
}
```

همانطور که مشاهده میشود `_id` رو هم برگردونده و درواقع داکیومنت درج شده در collection رو به طور کامل برگردوند

### Insert Many Document

با تابع `insertMany` چند داکیومنت اضافه میکنیم به کالکشن

```js
await ModelName.insertMany([
  {
    // item1 ...
  },
  {
    // item2 ...
  },
  // ...
]);
```

برای مثال برای درست کردن چند داکیومنت برای کالکشن users میتونیم از کد زیر استفاده کنیم

```js title="example"
const result = await UserModel.insertMany([
  {
    username: "amirhossein",
    password: "password1",
  },
  {
    username: "aliakbar",
    password: "password2",
  },
]);
```

مقدار result برابر آرایه‌ای از داکیومنت‌های ساخته شده است

```json title="result"
[
  {
    "username": "amabazari",
    "password": "amierqw132",
    "_id": "69ad708e127cb6086ca34d00",
    "__v": 0,
    "createdAt": "2026-03-08T12:50:22.490Z",
    "updatedAt": "2026-03-08T12:50:22.490Z"
  },
  {
    "username": "amabazari2",
    "password": "amierqw132",
    "_id": "69ad708e127cb6086ca34d01",
    "__v": 0,
    "createdAt": "2026-03-08T12:50:22.491Z",
    "updatedAt": "2026-03-08T12:50:22.491Z"
  }
]
```

### Find Document

همون وظیفه‌ی `SELECT` توی دیتابیس sql داره

```js
await ModelName.find();
```

اگیر بخوایم یک آیتم برگردونه میتونی از `findOne` استفاده کنی که فقط یک آبجکت برمیگردونه

```js
await ModelName.findOne();
```

توی این نمونه تمام داکیومنت‌های مدل `ModelName` رو برمیگردونه

میتونیم شرط بزاریم که چه داکیومنت‌هایی و چه بخش‌هایی از داکیومنت رو برگردونه

```js
await ModelName.find({ conditions }, { returns });
```

میتونیم شرط بزاریم برای آیتم‌هایی که برمیگردونه برای مثال آیتم‌هایی رو برگردونه که password اشون برابر علی باشه

```js
await UserModel.find({ password: "ali" });
```

بعضی کوئری‌ها هم میتونیم بزنیم

```js
await UserModel.find({ age: { $gte: 18 } }); // greater than equal : age >= 18
await UserModel.find({ age: { $gt: 18 } }); // greater than : age > 18
await UserModel.find({ age: { $lte: 18 } }); // less than equal : age <= 18
await UserModel.find({ age: { $lt: 18 } }); // less than : age < 18
await UserModel.find({ age: { $not: 32 } }); // less than : age != 32
await UserModel.find({ age: { $regex: / regex / } }); // less than : age == regex
```

میتونیم شرط بزاریم

میتونیم بگیم چه مقدایری از داکیومنت رو میخوایم

برای مثال اگر من توی نتیجه فقط `username` رو بخوام باید بزنم :

```js title="example"
const result = await UserModel.find({}, { username: true });
```

و مقدار result برابر زیر هست:

```json title="result"
[
  {
    "_id": "69ad61b013e6bde575e146f0",
    "username": "amabazari"
  },
  {
    "_id": "69ad6e7913b9d8b9c2953343",
    "username": "amabazari2"
  }
]
```

نکته: همواره مقدار `_id` برگردونده میشود

### Lean Document

به طور پیش‌فرض مقداری که برگردونده میشه شامل عبارت‌های زیادی هست اگر ما هنگام find کردن یک داکیومنت به انتها‌ی اون متد `lean` رو کال کنیم پرفورمنس MongoDB بالا میره و بهتر میتونه عمل بکنه و چیزایی که نیاز نداریم رو حذف میکنه

```js
await ModelName.findOne(condition).lean();
```

توی این حالت خیلی پرفورمنس بالاتر میره

برای مثال برای پیدا کردن یک یوزر با موبایلش به صورت زیر عمل میکنیم

```js title="example"
const user = await UserModel.findOne({ _id: id, mobile }).lean();
```

در اینجا مقدار خروجی user شامل آیتم‌هایی بهینه تر هست. دقت شود که یک ساختار داخلی هست و ما خیلی متوجهش نمیشیم

### Delete Document

برای حذف داکیومنت استفاده میشه که هم میتونیم از `deleteOne` و هم از `deleteMany` استفاده کنیم

- deleteOne :

```js
await ModelName.deleteOne({ condition });
```

- deleteMany :

```js
await ModelName.deleteMany({ condition });
```

برای مثال برای حذف یک یوزرهایی که اسمشون برابر امیرحسین و سنشون از ۱۸ سال کمتر هست میتونیم از کد زیر استفاده کنیم

```js
const result = await UserModel.deleteMany({
  name: "amirhossein",
  age: { $lt: 18 },
});
```

خروجی result ۲ مقدار برمیگردونه که دومی برابر تعداد داکیومنت‌هایی هست که حذف شدن

```json
{
  "acknowledged": true,
  "deletedCount": 12
}
```

توی نمونه‌ی بالا یعنی ۱۲ داکیومنت از کالکشن user حذف شده

### Update Document

برای آپدیت داکیومنت استفاده میشه که هم میتونیم از `updateOne` و هم از `updateMany` استفاده کنیم

- updateOne :

```js
await UserModel.updateOne(condition, updates);
```

- updateMany :

```js
await UserModel.updateMany(condition, updates);
```

توی condition درواقع باید مشخص کنیم چه داکیومنتی رو میخوایم آپدیت کنیم و توی updates باید مقادیر جدیدی که میخوایم رو طبق برخی اتتریبیوت ها بهش بدیم. برای مثال اگر میخوایم یک فیلدش رو عوض کنیم باید از `$set : {}` استفاده کنیم

برای مثال میخواهیم تمام کاربران زیر ۱۸ سال را پسوردشان رو عوض کنیم

```js
const result = await UserModel.updateOne(
  {
    age: { $lt: 18 },
  },
  {
    $set: { password: "new password" },
  },
);
```

مقدار result هم فرمتی مثل زیر داره :

```json
{
  "acknowledged": true,
  "modifiedCount": 1,
  "upsertedId": null,
  "upsertedCount": 0,
  "matchedCount": 1
}
```

در اینجا ما مقدار password را با استفاده از `set` عوض میکنیم. متد های دیگر مانند `unset`, `push`, `min`, `max` و ... وجود دارد که هرکدام کاربرد خودشون رو دارن.

### Find One And

ما یک متد به فرمت `findOneAnd` داریم که مقدار داکیومنت رو **قبل از اعمال تغییر** برمیگردونه

برای مثال اگر ما `findOneAndDelete` بزنیم داکیومنت رو حذف میکنه و توی result مقدار داکیومنت قبل از حذف رو میده

یا ما `findOneAndUpdate` اگر بزنیم داکیومنت رو اپدیت میکنه و مقدار داکیومنت رو قبل از آپدیت برمیگردونه

برای مثال اگر بخوایم پسورد یک کاربر رو که `mypass` بوده رو به `newpass` تغییر بدیم و از `findOneAndUpdate` استفاده کنیم مقدار پسورد درون مقدار result برابر `mypass` یا همون پسورد قبل از آپدیت هست

مقدار حال حاضر :

```json {4}
{
  "_id": "69ad708e127cb6086ca34d00",
  "username": "amabazari",
  "password": "mypass",
  "__v": 0,
  "createdAt": "2026-03-08T12:50:22.490Z",
  "updatedAt": "2026-03-08T14:22:21.468Z"
}
```

کد آپدیت :

```js {1}
const result = await UserModel.findOneAndUpdate(
  {
    _id: "69ad708e127cb6086ca34d00",
  },
  {
    $set: { password: "newpass" },
  },
);
```

مقدار `result`:

```json {4}
{
  "_id": "69ad708e127cb6086ca34d00",
  "username": "amabazari",
  "password": "mypass",
  "__v": 0,
  "createdAt": "2026-03-08T12:50:22.490Z",
  "updatedAt": "2026-03-08T14:22:21.468Z"
}
```

همانطور که مشاهده میشود متد `findOneAndUpdate` ابتدا مقدار را پیدا میکند سپس درون result قرار میدهد و درنهایت آپدیت میکند
