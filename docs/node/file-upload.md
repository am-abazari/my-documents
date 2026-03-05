---
sidebar_position: 5
---

# File Uploading

در این داکیومنت در رابطه با کتابخونه‌های مختلف برای آپلود فایل صحبت میکنیم

## Multer

روش پیشنهادی خود express.js برای آپلود فایل ها هست

کار باهاش **خیلی خیلی** راحت هست

### Installation

```
npm install multer --save
```

### Single File Upload

برای شروع کار با multer باید ابتدا مسیری که میخواهیم داخلش فایل آپلود بشه رو مشخص کنیم و به برنامه بدیم

سپس تعین کنیم اسم کلیدی که هنگام آپلود در form-data ارسال میکنیم چیست

```js
const multer = require("multer");

const uploadFile = multer({ dest: "uploads" }); // فولدر ذخیره سازی فایل ها

app.post("/api/upload", uploadFile.single("upload"), (req, res) => {
  res.json(req.file);
});
```

درواقع در این روش گفته شده که در `form-data` ارسال شده فایل مدنظر با کلید `upload` باید ارسال شود و از طرفی فایل های آپلود شده در دایرکتوری `uploads` ذخیره میشود

برای مسیر دهی بهتر میتونیم از ماژول `path` هم استفاده کنیم :

```js
const uploadFile = multer({ dest: path.join(__dirname, "..", "uploads") });
```

### Array of Files Upload

برای آپلود چند فایل صرفا باید از `array` به جای `single` استفاده کرد

یعنی به فرمت زیر باشد :

```js
uploadFile.array("upload", count);
```

`count` حداکثر تعداد فایل‌های قابل آپلود را تعیین میکند

#### مثال :

```js
const multer = require("multer");

const uploadFile = multer({ dest: "uploads" });

app.post("/api/upload", uploadFile.array("upload", 3), (req, res) => {
  res.json(req.file);
});
```

### Multer Config

ما میتونیم برای مولتر کانفیگ ست بکنیم که با چه اسمی ذخیره بکنه، حداکثر سایزش چقدر باشه، با توجه به هر فایل در کدام مسیر ذخیره بکنه و ....

در اینجا یک مثال ساده از کانفیگش آوردیم :

```js title="multer.middleware.js"
const multer = require("multer");
const path = require("path");
const fs = require("fs");

const multerStorage = multer.diskStorage({
  destination: (req, file, cb) => {
    // هرچیزی که توسط cb کال بشه اونجا فایل ذخیره میشود

    if (!fs.existsSync("uploads")) fs.mkdirSync("uploads");
    cb(null, "uploads"); // cb(error, destination)
  },
  filename: (req, file, cb) => {
    // هرچزیزی که توسط cb کال شود اسم فایل قرار میگیرد

    const basename = path.basename(file.originalname);
    cb(null, basename);
  },
});

const uploadFile = multer({
  storage: multerStorage,
  limits: {
    fieldSize: 1 * 1000 * 1000, // 1e6 Byte = 1MB
  },
});

module.exports = uploadFile;
```

در این کانفیگ دو مقدار `destination` و `filename` ذکر شده که هر مقداری توسط `cb(error, value)` در بخش `value` کال شود ذخیره میشود

و نحوه‌ی استفاده از آن تفاوتی نمیکند

```js
const uploadFile = require("./middlewares/multer.middleware.js");

app.post("/api/upload", uploadFile.array("upload", 3), (req, res) => {
  res.json(req.files);
});
```

:::info
اگر چند فایل آپلود میشود توسط `req.files` به آنها میتوان دسترسی داشت و اگر یک فایل است توسط `req.file` میتوان به آن دسترسی داشت
:::

### Make Directory Static

بعد از آپلود فایل در یک دایرکتوری باید به آن دسترسی داشته باشیم.

که توسط میدلور `express.static` این امر قابل پیاده سازی هست

```js
app.use(express.static("uploads"));
```

در این حالت دایرکتوری `uploads` رو به عنوان استاتیک و قابل دسترس تعریف میکنه

<br />
:::warning
بعد از استاتیک کردن یک فولدر نیاز به وارد کردن اسم فولدر توی url-ای که سرچ میکنیم نیست
:::

برای مثال اگر داخل دایرکتوری `uploads` یک فایل به اسم `img.png` باشد برای دسترسی به آن باید مسیر زیر رو زد

```bash title="/uploads/img.png/"
http://localhost:8000/img.png
```

و نیاز به وارد کردن `uploads` در مسیر نیست

### Multiple Fileds Upload

تفاوت این فیلد با بخشArray of Files Upload این هست که در اون بخش ما یک آرایه با کلید یکسان ارسال میکردیم اما در این بخش میتونیم بای کلیدهای متفاوت ارسال کنیم

در این حالت باید از `uploadFile.fileds([])` استفاده کنیم که هر فیلد را مشخص کنیم

**برای مثال** یک آرایه از فایل‌ها با کلید `upload` و حداکثر ۳ فایل و یک فایل با کلید `image` و یکسری داکیومنت با کلید `documents` و حداکثر ۶ داکیومنت ارسال کنیم :

```js
app.post(
  "/api/upload",
  uploadFile.fields([
    {
      name: "upload",
      maxCount: 3,
    },
    {
      name: "image",
      maxCount: 1,
    },
    {
      name: "documents",
      maxCount: 6,
    },
  ]),
  (req, res) => {
    res.json(req.files);
  },
);
```

### Any File Upload

در این متد میگوییم فرقی نمیکند چه فایلی و با چه نوعی ارسال شود. هرفایلی را با هر کلید فیلدی قبول میکند

که باید از `uploadFile.any()` به عنوان میدلور استفاده کرد

```js
app.post("/api/upload", uploadFile.any(), (req, res) => {
  res.json(req.files);
});
```

### None File Upload

در این متد هیچ فایلی حق آپلود کردن ندارد

که باید از `uploadFile.none()` به عنوان میدلور استفاده کرد

```js
app.post("/api/upload", uploadFile.none(), (req, res) => {
  res.json(req.files);
});
```
