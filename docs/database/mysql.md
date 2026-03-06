---
sidebar_position: 1
---

# MySQL

در این بخش در رابطه با دیتابیس MySQL و زبان توسعه‌ی آن صحبت میکنیم

یک دیتابیس رابطه‌ای(relational) هست که توسط یک شخص ساخته شده. صاحبت mysql و mariadb یک شخص هست که my و maria اسم بچه‌هاش بودن

اینجا یسری از کامندها mysql رو میگیم که مربوط به زبان خود mysql هست

<br />
<br />

## Database Commands

### Show Database

تمام دیتابیس‌هایی که داریم رو نشون میده

```sql
SHOW DATABASES;
```

### Create Database

یک دیتابیس با نام دلخواه میسازد

```sql
CREATE DATABASE db_name;
```

- ساخت دیتابیس درصورت عدم وجود:

```sql
CREATE DATABASE IF NOT EXISTS db_name;
```

### Delete Database

دیتابیس با نام داده شده را حذف میکند

```sql
DROP DATABASE db_name;
```

- حذف دیتابیس درصورت وجود:

```sql
DROP DATABASE IF EXISTS db_name;
```

### Collection Config Database

نوع کالکشن‌های دیتابیس را مشخص میکند

```sql
CREATE DATABASE db_name COLLATE utf8mb4_general_ci;
```

`utf8mb4_general_ci` هم از فارسی و هم از انگلیسی و ایموجی پشتیبانی میکنه و از طرفی `ci` به معنی case insensitive هست یعنی به بزرگ و کوچک بودن کلمات اهمیت نمیده

### Focus Database

اگر داریم sql کد میزنیم و نیاز داریم روی یک دیتابیس خاص فوکوس کنیم به طوری که کامند های بعدیمون روی این دیتابیس اعمال بشه از این عبارت استفاده میکنیم

```sql
USE db_name;
```

از این به بعد هر کامندی بزنیم روی دیتابیس `db_name` اعمال میشه

<br />
<br />

## Tables Commands

### Create Table

روی یک دیتابیس که از قبل focus شده بود یک table جدید میسازد

```sql
CREATE TABLE table_name (fields);
```

```sql title="example"
CREATE TABLE table_name (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    password VARCHAR(50) NOT NULL,
    username VARCHAR(50) UNIQUE NOT NULL
);
```

- ساخت table درصورت عدم وجود:

```sql
CREATE TABLE IF NOT EXISTS table_name (fields);
```

### Delete Table

روی دیتابیس در ابتدای کار باید focus شود سپس table مدنظر را حذف میکند

```sql
DROP TABLE table_name;
```

- حذف table درصورت وجود:

```sql
DROP TABLE IF EXISTS table_name;
```

### Truncate Table

truncate تمام آیتم‌های یک table رو حذف میکنه و از طرفی آیدی‌هارو ریست میکنه.

دستی حذف کردن تمام آیتم‌های یک table باعث ریست شدن آیدی‌ها نمیشه درحالی که truncate کل table رو خالی میکنه و آیدی‌هارو ریست میکنه

معادل اینکار حذف کل table و ساختن مجددش هست درحالی که با truncate نیاز نیست حذف کنیم و صرفا دیتای‌ داخلش رو پاک میکنه

```sql
TRUNCATE TABLE table_name;
```

### Focus Table

شاید نیاز شود ما یک ستون یا سطر به یک table اضافه کنیم. در این صورت ابتدا باید روی دیتابیس focus شود و سپس روی table که برای table از کامند زیر استفاده میکنیم

```sql
ALTER TABLE table_name;
```

### Enum Column

کلا مفهوم enum انتخاب بین چند آپشن ثابت هست

و هنگام ساختن table یک آیتم رو ENUM میکنیم

```sql title="example" {4}
CREATE TABLE table_name (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    role ENUM("admin", "user", "super-user")
);
```

در این مثال role فقط یکی از ۳ آپشن داده شده میتونه باشه

<br /> <br />

### Insert

داخل یک table که از قبل focus شده روش یک سطر اضافه میکنیم

```sql
INSERT INTO table_name (keys) VALUES (value1), (value2), ..., (valueN)
```

با این روش میتوینم چند مقدار داخل table_name بریزیم

```sql title="example"
INSERT INTO table_name (name, password, username) VALUES
        ("Amirhossein", "amirhossein1384", "am_abazari"),
        ("Ali", "AliPassword!(8", "ali_hosseini"),
        ("Mahsa", "mahsa0031nd!3", "mahsa_hs");
```

در این مثال ۳ مقدار ریخته میشود.

ما باید فیلد‌هایی که میخوایم بریزیم رو توی قسمت keys وارد کنیم و مقادیر مدنظر رو در قسمت values داخل یک پرانتر وارد کنیم که هرکدام متناظر با کلید داده شده است

### Update

```sql
UPDATE table_name SET key="value" WHERE key2="value2"
```

در این روش بهتر است مقدار `key2=value2` یونیک باشد در صورتی که چند ردیف پیدا بکند که شر `key2=value2` در آنها برقرار باشد روی همه اعمال میکند

#### AND

با استفاده از `AND` میتونیم شرطمون رو قوی تر بکنیم

```sql
UPDATE table_name SET key="value" WHERE key2="value2" AND key3="value3" AND ...
```

در این مثال هرجایی که تمام شرط ها برقرار باشند مقدار ستونی را که کلیدش برابر `key` هست را برابر `value` قرار میدهد

#### OR

با استفاده از `OR` میتونیم حالات دیگر رو هم درنظر بگیریم

```sql
UPDATE table_name SET key="value" WHERE key2="value2" OR key3="value3" OR ...
```

در این مثال هرجایی که یکی از شرط ها برقرار باشه عملیات رو انجام میده

**نکته:** همیشه توی تمام زبان‌های برنامه نویسی ابتدا AND اجرا میشه و سپس OR

#### NOT

با استفاده از NOT میگیم که اگر عبارت برابرش نبود آپدیت کن برای مثال

```sql
UPDATE table_name SET key="value" WHERE NOT key2="value2"
```

NOT فقط روی اولی اعمال میشه

#### Example

```sql title="example"
UPDATE table_name SET name="Amirhossein", password="new paaswrd" WHERE username="am_abazari" OR id=32 AND NOT id=42
```

### Delete row

یک رکورد(سطر) از یک table رو میخوایم حذف کنیم

```sql
DELETE FROM table_name WHERE Condition...
```

عبارت `Condition` همون شرط‌هایی هست که با استفاده از AND یا OR و ... استفاده میشه

#### Example:

```sql title="example"
DELETE FROM table_name WHERE id>=8 AND id<11 OR id=32
```

### Select

اینجا رکورد‌های (سطرها) یک table رو میخونیم

#### Read All Records

تمامی رکورد هارا میخونیم

```sql
SELECT * FROM table_name
```

#### Condition

شرط قرار میدیم برای دریافت رکوردها

```sql
SELECT * FROM table_name WHERE Condition...
```

Condition همان شرط‌هایی هست که میتونیم اعمال کنیم
برای مثال :

```sql
SELECT * FROM table_name WHERE age>=18 OR city="tehran"
```

#### Sort

توی اینجای دیتایی که میگیریم رو طبق یک کلید(ستون) مرتبط سازی میکنیم

۲ حالت داره مرتبط سازی:

1. `DESC` که از بزرگ به کوچک هست
2. `ASC` از کوچک به بزرگ هست

```sql
SELECT * FROM table_name ORDER BY DESC
```

#### Min, Max, Average, Count, Sum

توی یک table ما میتونیم مینیمم، ماکسیمم، میانگین، تعداد و مجموع یک ستون رو بدست بیاریم

```sql
SELECT MIN(column_name) FROM table_name
```

```sql
SELECT MAX(column_name) FROM table_name
```

```sql
SELECT AVG(column_name) FROM table_name
```

```sql
SELECT SUM(column_name) FROM table_name
```

```sql
SELECT COUNT(column_name) FROM table_name
```

#### As New Name

ما فیلدی که بدست اومده رو میتونیم به یک اسم جدید و دلخواه ذخیره کنیم

برای مثال اگر ما دنبال کمترین سن هستیم میتونیم اونو به یک اسم جدید برگردونیم

```sql
SELECT MIN(age) AS minAge FROM table_name
```

و وقتی mysql عبارت رو برگردونه با کلید minAge برمیگردونه

#### Like

یک اوپراتور برای سرچ و select هست

برای مثال

```sql
SELECT * FROM table_name WHERE column_name LIKE LikeOptions
```

عبارت LikeOptions یسری فرمت برای سرچ هست که درصورتی که فیلد `column_name` توی اون صدق کنه برمیگردونتش

- `xyz%` : یعنی شروع فیلد با xyz باشه و بقیش مهم نیست
- `%xyz` : پایان فیلد با xyz باشه و بقیش مهم نیست
- `%xyx%` : وسطش عبارت xyz باشه
- `c_ch_go`: ـ ها میتونن هرچیزی باشن. برای مثال عبارت cichago توی این صدق میکنه

```sql title="example"
SELECT * FROM table_name WHERE city LIKE "%cago" OR name LIKE "john%"
```

توی این مثال یا باید عبارت city با cago تموم بشه و یا اسم با john شروع بشه

#### In, Between

- `BETWEEN` : باید آیتم مدنظر بین دو بازه‌ی داده شده باشه
- `IN`: یک لیست میدیم و آیتم مدنظر باید داخل لیست باشه

```sql
SELECT * FROM table_name WHERE age BETWEEN 25 AND 30;
```

آیتم‌هایی رو سلکت میکنه که مقدار age اشون بین ۲۵ و ۳۰ باشه

```sql
SELECT * FROM table_name WHERE city IN ("new york", "los angeles", "austin");
```

آیتم‌هایی رو سلکت میکنه که city یکی از مقادیر داده شده باشه

#### Any, All

- `ANY` : باید حداقل شرط برای یکی از آیتم‌های لیستی که میدیم برقرار باشه
- `ALL` : باید شرطی که میدیم برای تمام آیتم‌های لیست برقرار باشه

```sql
SELECT * FROM table_name WHERE age > ANY(12, 14, 16, 20);
```

توی این مثال اگر age بزرگتر از یکی از آیتم‌ها باشه حداقل اونو برمیگردونه. توی اینجا یعنی باید حداقل از ۱۲ بزرگتر باشه

```sql
SELECT * FROM table_name WHERE age > ALL(12, 14, 16, 20);
```

توی این مثال باید age از تمام آیتم‌ها بزرگتر باشه یعنی باید از ۲۰ بزرگتر باشه اونوقت از همه بزرگتر هست

#### Limit

این متد برای pagination استفاده میشه و برای گرفتن دیتا به صورت مرحله به مرحله

۲ متد میگیره برای pagination

1. `LIMIT` : تعدادی که باید نشون بده
2. `OFFSET` : از جایی که باید شروع کنه برای نشون دادن (به صورت دیفالت صفر باید باشه)

```sql
SELECT * FROM table_name LIMIT 10 OFFSET 0;
```

۱۰ آیتم اول رو میده

```sql
SELECT * FROM table_name LIMIT 10 OFFSET 10;
```

۱۰ آیتم دوم رو میده و همینطور میتونیم ادامه بدیم و ۱۰ تا ۱۰ تا اضافه کنیم ...

<br />
<br />

## Columns Commands

### Create Column

یک ستون به table-ای که از قبل روی آن focus شده بود اضافه میکند با مشخصات مدنظر

```sql
ADD column_name Options ...
```

بخش Options همان نوع ستون و null بودن یا نبودن و ... را مشخص میکند

```sql title="example"
ALTER TABLE table_name
ADD coumn_name VARCHAR(100) NOT NULL UNIQUE
```

### Delete Column

یک ستون درون یک table که از قبل روی آن focus شده را حذف میکند

```sql
DROP COLUMN column_name;
```

```sql title="example"
ALTER TABLE table_name
DROP COLUMN column_name;
```

### Edit Column

یک ستون از یک table که روی آن focus شده را تغییر میدهد مقادیرش را

برای مثال قبلا میتونست null باشه ولی الان نمیتونه پس NOT NULL اضافه میکنیم بهش

```sql
MODIFY COLUMN column_name Options...
```

```sql title="example"
ALTER TABLE table_name
MODIFY COLUMN column_name DATE NOT NULL UNIQUE DEFAULT CURRENT_DATE
```

:::info
اب عبارت DEFAULT میتونیم برای ست کردن ساعت حال حاضر هم استفاده کنیم:

```sql
DEFAULT CURRENT_TIME;
```

`CURRENT_TIME`, `CURRENT_DATE`, `CURRENT_TIMESTAMP` هم قابل استفاده هست
که باید متناظر با اونا تایپ یک column هم باشه

یعنی :

```sql
MODIFY COLUMN column_name DATE DEFAULT CURRENT_DATE
MODIFY COLUMN column_name TIME DEFAULT CURRENT_TIME
MODIFY COLUMN column_name TIMESTAMP DEFAULT CURRENT_TIMESTAMP
```

:::

<br />
<br />

## Constraints

محدودیت های قابل اعمال هستش روی دیتابیس که با تابع `CHECK` انجام میشود

```sql example
CREATE TABLE table_name (
  id  INT PRIMARY KEY AUTO_INCREMENT,
  age INT NOT NULL,
  CHECK ( age >= 18 )
);
```

## Index

زمانی که دیتای یک دیتابیس خیلی سنگین میشود ما روی یک ستون index قرار میدیم که سرچ سریعتر بتونیم انجام بدیم. درواقع ترجیحا باید یونیک باشه که عملیات سرچ توسط binary_search بهتر انجام بشه

دو روش داره index گذاری یکی هنگام ساخت table و یکی هم اضافه کردن ایندکس به صورت دستی

:::info
خود sql به‌صورت پیشفرض روی آیتم‌هایی که Primary Key هستن یا unique هستن index گذاری انجام میده
:::

### While Creating Table

هنگام ساخت یک table به ستون مدنظرمون index اضافه میکنیم

```sql title="example" {6}
CREATE TABLE table_name (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username varchar(100),
    fullname VARCHAR(100),
    phone VARCHAR(20),
    INDEX(phone, username)
);
```

در اینجا ما مشخص کردیم که ۲ فیلد `username` و `phone` ایندکس گذاری بشن که درنتیجه باعث خیلی سریعتر شدن سرچ میشه

### Add Manual Index

به صورت دستی هم میتوان یک column رو بهش index اضافه کرد با روش زیر :

```sql
CREATE INDEX index_name ON table_name (column1, column2, ...)
```

در این روش یک index با نام index_name روی table با نام table_name روی ستون‌های column1 تا ... اعمال میشه

مثال بالا رو میتوان به اینصورت هم گفت :

```sql title="example"
CREATE INDEX phone_and_username_index  ON table_name (username, phone)
```

در این روش هم دقیقا به ستون‌های `phone` و `username` درون `table_name` یک ایندکس با نام `phone_and_username_index` اضافه میشود

### Delete Index

اگر یک ایندکس ساختیم و خواستیم حذفش کنیم باید از روش زیر استفاده کنیم

```sql
DROP INDEX index_name ON table_name;
```

مثال :

```sql title="example"
DROP INDEX phone_and_username_index ON table_name;
```
