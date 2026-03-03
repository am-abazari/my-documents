---
sidebar_position: 2
---

# ماژول‌های پیش‌فرض

در این داکیومنت در رابطه با ماژول‌هایی که به صورت پیش‌فرض درون node.js هستند صحبت میکنیم

## http

### start server

برای ران کردن یک سرور در nodejs راه‌های مختلفی وجود دارد. ابتدایی ترین روش استفاده از ماژول http هست که به صورت دیفالت درون nodejs وجود دارد

این فایل صرفا وظیفه‌ی ران کردن اولیه‌ی سرور رو دارد و باقی لاجیک برنامه باید طبق مدل های MVC , ... پیش بره

```js title="server.js"
const http = require("http");

// constants
const PORT = 8000;

const server = http.createServer((req, res) => {
  res.write("hello world!");
  res.end();
});

server.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

در این حالت سرور روی پورت 8000 ران میشود

## fs

ماژول fs مخفف file system است که وظیفه‌ی کار با فایل‌های سیستم رو داره

### read file

برای خواندن یک فایل ۲ روش وجود دارد. یکی به صورت sync و دیگری به صورت async

درحالتی که نیاز به اطلاعات فایل داشته باشیم باید از sync استفاده کنیم و درصورتی که نیاز نداشته باشیم میتونیم از async استفاده کنیم

#### sync

```js
const fs = require("fs");

try {
  const data = fs.readFileSync("file.txt", "utf-8");
  console.log(data);
} catch (error) {
  console.log(error);
}
```

#### async

```js
const fs = require("fs");

fs.readFile("file.txt", (error, data) => {
  if (error) console.log(error);
  else console.log(data.toString());
});
```

:::warning
در nodejs بیس مسیرها توی جایی هست که فایل `package.json` قرار داره

برای مثال اگر ما دنبال فایل `data/file.txt/` باشیم و خودمون توی دایرکتوری `/` باشیم، اگر بزنیم `fs.readFile("../data/file.txt)` ارور میده چون به صورت پیش فرض به جایی اشاره میکنه که فایل `package.json` هست و وقتی `../` میزنیم از فولدر پروژه بیرون میزنه

پس درحالت کلی مسیر هارو به صورت absolute بده و نه relative و یا از ماژول path استفاده کن که خیلی راحتتره

یا از `proccess.cwd()` استفاده کن که دقیق اشاره میکنه به root route
:::

### write file

در نوشتن فایل هم ۲ روش وجود دارد که یکی به صورت sync و async که درصورتی که نیاز به فایل نوشته شده داریم باید از sync و در غیر اینصورت از async استفاده کرد

**نکته:** همیشه باید دیتایی که برای نوشتن داده میشود به صورت string باشد.

اگر بخوایم داخل فایل json بنویسیم باید ابتدا stringify بکنیم

#### sync

```js
const fs = require("fs");

const data = "hello world!";

fs.writeFileSync("write.txt", data, (err) => console.log(err));
```

#### async

```js
const fs = require("fs");

const data = "hello world!";

fs.writeFile("write.txt", data, (err) => console.log(err));
```

:::info
writeFile و writeFileAsync اگر فایل وجود نداشته باشه اونو اول میسازن بعد داخلش مینویسن
:::

#### flags

برخی flag ها و آپشن ها برای نوشتن فایل وجود دارد که `flag: "a"` برای مثال به معنی اضافه کردن به ته فایل هست

```js
fs.writeFile("write.txt", data, { flag: "a", encoding: "utf-8" }, (err) =>
  console.log(err),
);
```

در این حالت ما `flag` رو برابر `a` قرار دادیم که به معنی append هست و به انتهای فایل `write.txt` متن رو اضافه میکنه

### append file

این تابع به انتهای یک فایل دیتای مد نظر رو اضافه میکنه

عملکرد این تابع مانند تابع `writeFile` همراه با `flag` برابر `a` هست

#### async

```js
const fs = require("fs");

const data = "hello world!\n";

fs.appendFile("write.txt", data, (err) => console.log(err));
```

#### sync

```js
const fs = require("fs");

const data = "hello world!\n";

fs.appendFileSync("write.txt", data, (err) => console.log(err));
```

### remove file

برای حذف یک فایل باید از تابع `unlink` استفاده کرد

#### async

```js
const fs = require("fs");

fs.unlink("write.txt", (err) => console.log(err));
```

#### sync

```js
const fs = require("fs");

fs.unlinkSync("write.txt", (err) => console.log(err));
```

### exist file

این تابع فقط حالت sync رو دارد چون اگر async باشد اصلا کال کردن تابع بی معنی هست

```js
const fs = require("fs");

const isExist = fs.existsSync("write.txt");
console.log(isExist);
```

برای مثال میتونیم چک کنیم که اگر فایل وجود داشت حذفش بکنه و یا اگر وجود نداشت بسازه

```js
const fs = require("fs");

const isExist = fs.existsSync("src/write.txt");

if (isExist) fs.unlink("src/write.txt", (err) => console.log(err));
else fs.writeFile("src/write.txt", "file created!", (err) => console.log(err));
```

### create directory

برای ساختن فولدر استفاده میشود

#### async

```js
const fs = require("fs");

fs.mkdir("parent/child/childs-child", (err) => console.log(err));
```

#### sync

```js
const fs = require("fs");

fs.mkdirSync("parent/child/childs-child", (err) => console.log(err));
```

در این روش ابتدا چک میکنه `parent` وجود داره یا نه و اگر وجود نداشت میسازه سپس همینطوری به صورت بازگشتی تا اخرین فولدر میره و درصورتی که تمام فولدر ها وجود داشته باشن ارور میده و دغیراینصورت همرو میسازه

برای رفع ارور میتونیم از یک آپشن استفاده کنیم :

```js
fs.mkdirSync("parent/child/childs-child", { recursive: true }, (err) =>
  console.log(err),
);
```

توی این حالت اگر که تمام فولدر ها هم وجود داشته باشن ارور نمیده به خاطر وجود `recursive: true`

### read directory

این تابع تمام دایرکتوری‌ها و فایل‌های داخل یک دایرکتوری رو برمیگردونه

#### async

```js
const fs = require("fs");

fs.readdir("parent/child/childs-child", { recursive: true }, (err, dir) => {
  if (err) console.log(err);
  else console.log(dir);
});
```

#### sync

```js
const fs = require("fs");

fs.readdirSync("parent/child/childs-child", { recursive: true }, (err, dir) => {
  if (err) console.log(err);
  else console.log(dir);
});
```

## path

توی انواع سیستم عامل‌های مختلف مسیردهی‌ها متفاوت هست برای مثال توی ویندوز داریم `C:\Public` درحالی که توی لینوکس و مک داریم `/apt/user/` برای حل این مشکل از ماژول path استفاده میکنیم که یک کد رو مینویسم و خودش تشخیص میده

### separator

همونطور که گفتم توی ویندوز از `\` ولی توی لینوکس و مک از `/` استفاده میشود که این همون رو نشون میده

```js
const path = require("path");

console.log(path.sep + "src" + path.sep + "app" + path.se);
```

خروجی این کد برابر `/src/app/` توی لینوکس و مک هست برای مثال

### basename

اخرین فایل یا دایرکتوری مدنظر رو نشون میده

```js
const path = require("path");

console.log(path.basename("/src/app/index.js"));
```

خروجی کد برابر `index.js` هست

توی این تابع میتونیم یک suffix از خروجی رو هم داشته باشیم. برای مثال اگر من فقط اسم فایل رو بخوام میتونم بگم که `.js` رو حذف کنه که به صورت زیر انجام میشه :

```js
const path = require("path");

console.log(path.basename("/src/app/index.js", ".js"));
```

### dirname

اسم تمام دایرکتوری‌هی مسیر داده شده رو میگه. دقت کن که آخرین بخش مسیر رو هیچوقت نمیگه حتی اگر اکستنشن نداشته باشه

```js
const path = require("path");

console.log(path.dirname("/src/app/index.js"));
```

خروجی این کد برابر `/src/app` هست. حتی درصورتی که `index` به جای `index.js` قرار بدیم خروجی فرقی نمیکنه

### extname

نوع اکستنشن فایل رو میگه. برای مثال برای فایل `index.jsx` به ما `.jsx` برمیگردونه

```js
const path = require("path");

console.log(path.extname("/src/app/index.js"));
```

خروجی این کد برابر `.js` است

**مثال**: اگر فقط اسم فایل رو بخوایم و نوع اکستنشن رو ندونیم و بتونیم از ۲ تابع `extname` و `basename` استفاده کنیم چه باید کرد؟

```js
const path = require("path");

const pt = "/src/app/index.js";
console.log(path.basename(pt, path.extname(pt)));
```

خروجی این کد برابر `index` هست

درواقع ما suffix-ای رو میگیریم که مقدار `extname` رو نداشته باشه

### join

برای چسبوندن چند مسیر به هم استفاده میشود که با توجه به نوع سیستم عامل ممکنه متغیر باشد

```js
const path = require("path");

console.log(path.join("/", "src", "app", "components"));
```

خروجی این کد برابر`/src/app/components` است

### normalize

برای رفع ایرادهای یک مسیر استفاده میشود. ممکنه یجا دوتا // بزنیم و ... که اینارو هندل میکنه

```js
const path = require("path");

console.log(path.normalize("//src//app/"));
```

خروجی این کد برابر‍ `/src/app/` است

### parse

یک مسیر رو پارس میکنه و توی یک آبجکت جیسون نگه میداره

```js
const path = require("path");

console.log(path.parse("/src/app/component/index.js"));
```

خروجی این کد برابر :

```json
{
  "root": "/",
  "dir": "/src/app/component",
  "base": "index.js",
  "ext": ".js",
  "name": "index"
}
```

### dirname

فایلی که درحال حاضر درحال ران هست رو مسیر دایرکتوری‌اش رو نشون میده به صورت absolute در کل سیستم عامل

:::info
این مقدار به صورت global در node.js وجود داره و ربطی به ماژول path نداره
اما چون مربوط به مسیردهی بود اینجا ذکر شد
:::

```js
console.log(__dirname);
```

برای مثال خروجی این کد در سیستم عامل من به صورت `/Users/amirhossein/Documents/Programming/Node/Botostart/src/` است

### filename

فایلی که درحال حاضر درحال ران هست رو مسیر خودش رو نشون میده به صورت absolute در کل سیستم عامل

:::info
این مقدار به صورت global در node.js وجود داره و ربطی به ماژول path نداره
اما چون مربوط به مسیردهی بود اینجا ذکر شد
:::

```js
console.log(__filename);
```

برای مثال خروجی این کد در سیستم عامل من به صورت `/Users/amirhossein/Documents/Programming/Node/Botostart/src/index.js` است

**مثال**: برای مثال اگر ما توی `/src/app/` باشیم و بخوایم یک عکس توی `/src/assets/images/` رو برگردونیم باید به صورت زیر عمل کنیم

```js
const path = require("path");

console.log(path.join(__dirname, "..", "assets", "images"));
// ---- or ----
console.log(path.join(__filename, "..", "..", "assets", "images"));
```

## os

ماژول برای اطلاعات سیستم عامل هست

```js
const os = require("os");

const myOS = {
  name: os.type(),
  arch: os.arch(),
  platform: os.platform(),
  relase: os.release(),
  version: os.version(),
  uptime: os.uptime(), // نشون میدهد چند ثانیه است که سیستم روشن است
  threads: os.cpus(), // ترد های cpu رو میگه
};

console.log(myOS);
```

خروجی این برنامه روی سیستم من به صورت زیر هست :

```json
{
  "name": "Darwin",
  "arch": "rm64",
  "platform": "darwin",
  "relase": "25.3.0",
  "version": "Darwin Kernel Version 25.3.0: Wed Jan 28 20:48:41 PST 2026; root:xnu-12377.81.4~5/RELEASE_ARM64_T6041",
  "uptime": 19956,
  "threads": [
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] },
    { "model": "Apple M4 Pro", "speed": 2400, "times": [Object] }
  ]
}
```
