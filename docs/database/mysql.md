---
sidebar_position: 2
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

### Constraints

محدودیت های قابل اعمال هستش روی دیتابیس که با تابع هنگام ساخت دیتابیس با`CHECK` انجام میشود

```sql example
CREATE TABLE table_name (
  id  INT PRIMARY KEY AUTO_INCREMENT,
  age INT NOT NULL,
  CHECK ( age >= 18 )
);
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

## Triggers

جزو مباحث پیشرفته‌ی MySQL هست.

### Start

وظیف‌ی Trigger این هست که زمانی که یک تغییر در دیتابیس رخ میدهد قبل از اعمال تغییر یا بعد از آن یک عملیاتی را انجام میدهد

اون تغییر هرچیزی میتونه باشه. مثل INSERT, UPDATE, SELECT, DROP و ...

### Before

در این نمونه زمانی که یک ردیف در یک table ما INSERT میکنیم یک عملیات انجام میشود و نکته این هست که قبل از انجام عملیات INSERT عملیات مدنظر ما انجام میشود و سپس INSERT میشود

```sql
CREATE TRIGGER before_insert BEFORE INSERT ON table_name FOR EACH ROW
BEGIN
	My Job ...
END;
```

توی این مثال به ازای هر درج سطر درون table_name یک تریگر با نام `before_insert` که دلخواه هست روش انجام میشه که کار مدنظر من رو انجام میده

برای مثال اگر ما یک فرم برای ثبت نام یوزر داشته باشیم و بخوایم درصورتی که کاربر مقدار country رو خالی بزاره اونو برابر iran بکنیم قبل از هر عملیات ساخت یوزر باید به شکل زیر عمل کنیم :

```sql title="example"
DELIMITER $$

CREATE TRIGGER set_country_trigger BEFORE INSERT ON users FOR EACH ROW
BEGIN
    IF NEW.country IS NULL THEN SET NEW.country = 'iran';
    END IF;
END$$

DELIMITER ;
```

:::info
با آبجکت `NEW` میتونیم به سطری که میخوایم ثبت کنیم دسترسی داشته باشیم

و `DELIMITER` مقدار جدا کننده هست که در این مثال برای قاطی نشدنشون عوضش کردیم
:::

توی این مثال هنگام INSERT درون تیبل users درصورتی که سطر مدنظر مقدار country-اش خالی باشه اونوقت اونو iran ست میکنه

میتونیم مثلا بزاریم وقتی کشورش اسرائیل بود اونو خالی ست بکنه

```sql
IF NEW.country="israel" THEN SET NEW.country = '';
```

### After

این متد برای زمانی هست که ما بخوایم یک سطر درج بکنیم که پس از درج کردن سطر یک عملیات رو انجام میدهد همچنین این trigger با نامی که ست میکنیم براش قابل دسترس هست

فرقش با حالت قبلی اینه که جای BEFORE و AFTER باید عوض بشه

```sql
CREATE TRIGGER after_insert AFTER INSERT ON table_name FOR EACH ROW
BEGIN
	My Job ...
END;
```

برای مثال درصورتی که یک کالا به فروشگاه اضافه شود باید تعداد کل موجودی انبار عوض شود و برای اینکار میتونیم از نمونه‌ی زیر استفاده کنیم:

```sql title="example"
DELIMITER $$

CREATE TRIGGER update_store_quantity AFTER INSERT ON products FOR EACH ROW
BEGIN
    UPDATE store SET quantity=quantity+NEW.count;
END$$

DELIMITER ;
```

`quantity=quantity+NEW.count` همون معنی `quantity+=NEW.count` رو میده چون داخل sql مستقیم به مقداری که میخوایم تغییر بدیم دسترسی داریم
:::warning
شما نمیتونی روی جدولی که روش trigger ست کردی تغییر اعمل کنی.

یعنی اگر یک جدول تغییر کرد دیگه نیمتونی توی trigger-اش روی همون جدول تغییر اعمال کنی
:::

### Other Options

همینطور علاوه بر `AFTER INSERT` و `BEFORE INSERT` میتونیم روی بقیه عملیات ها مثل `DELETE` یا `UPDATE` و `SELECT` هم انجام داد

برای مثال در اینجا یک نمونه آورده شده که درواقع فرآیند validation سن در sql هست

```sql {6,7}
DELIMITER $$

CREATE TRIGGER validate_age BEFORE UPDATE ON users FOR EACH ROW
BEGIN
    IF(NEW.age < OLD.age) THEN
    SIGNAL SQLSTATE '45000'
    SET MESSAGE_TEXT="age must be bigger than old age";
    END IF;
END$$

DELIMITER ;
```

توی کد بالا چک میکنه که سن جدید از سن قدیمی باید بیشتر باشه وگرنه ارور برمیگردونه
:::info
در عبارت بالا ۲ متد `SIGNAL SQLSTATE` و `MESSAGE_TEXT` استفاده شده که برای برگردوندن ارور هست

- SQLSTATE : یسری ارور کد ها مختلف برای SQL هست که هرکدام معنی خودشون رو داره
- MESSAGE_TEXT : یک متن گلوبال توی SQL هست که متن ارور رو مشخص میکنه
  :::

## Relations

یک مبحث پیشرفته‌ی دیگر درون MySQL است

### Start

مفهوم این مبحث این هست که ارتباط میان table های یک دیتابیس وجود داشته باشد. که دقیقا نقطه‌ی قوت دیتابیس‌های Relational نسبت به MongoDB و امثال آن است.

برای مثال ما برای یک کاربر نام کاربری، پسورد، رمزعبور تصویر پروفایل آدرس و ... داریم. اگر تمام این موارد درون یک table باشن آنگاه هنگام گرفتن دیتا خیلی دیتای اضافه هم دریافت میشود. پس ما ۲ table جداگانه که یکی برای user و یکی برای profile هست میسازیم به طوری که میان این ۲ table ارتباط وجود دارد. هر کاربر یک سطر درون profile دارد و یک سطر درون user و این دو به هم متصل هستند

### Foreign Key

با استفاده از این متد ما بین یک فیلد یک table با یک فیلد یک table دیگر ارتباط (relation) برقرار میکنیم و در ادامه نحوه‌ی دریافت با توجه به ارتباط آنها را میگیم

درکل با متد `FOREIGN KEY` مشخض میشه و هنگام ساختن دیتابیس یک فیلد از اون رو به یک فیلد دیگر یک دیتابیس دیگر `REFERENCES` میدیم

```sql
FOREIGN KEY (field_name) REFERENCES `table_name`(other_field_name)
```

برای مثال ما ۲ دیتابیس یکی برای user و یکی برای profile میسازیم که profile باید به user مرتبط شود

```sql {15}
CREATE TABLE `user` (
    id INT PRIMARY KEY AUTO_INCREMENT,
    fullname VARCHAR(100) NOT NULL,
    username VARCHAR(100) NOT NULL,
    password VARCHAR(100) NOT NULL
);

CREATE TABLE `profile` (
    id INT PRIMARY KEY AUTO_INCREMENT,
    age INT,
    bio TEXT,
    image VARCHAR(50) DEFAULT "person.png",
    city VARCHAR(50),
    userID INT NOT NULL UNIQUE,
    FOREIGN KEY (userID) REFERENCES `user`(id)
);
```

در این مثال ما فیلد `userID` از تیبل profile رو به فیلد `id` از تیبل user رفرنس میدیم

### Multiple Foreign Key

یک فیلد میتونه با چند فیلد ارتباط داشته باشه و بلعکس یعنی چند فیلد میتونن با یک یا چند فیلد ارتباط داشته باشن

برای اینکار صرفا باید چند `FOREIGN KEY` قرار داد

```sql
FOREIGN KEY (field_1) REFERENCES `tabled_1`(field_2),
FOREIGN KEY (field_3) REFERENCES `tabled_2`(field_4),
...
```

برای مثال یک سفارش محصول به محصول و خریدار که یک یوزر هست ارتباط داره:

```sql title="example" {8,7}
CREATE TABLE `orders` (
    id INT PRIMARY KEY AUTO_INCREMENT,
    amount DOUBLE NOT NULL,
    description TEXT,
    userID INT NOT NULL UNIQUE,
    productID INT NOT NULL UNIQUE
    FOREIGN KEY (userID) REFERENCES `users`(id)
    FOREIGN KEY (productID) REFERENCES `products`(id)
);
```

### Inner Join

اگر دو فیلد از دو table متفاوت به هم ارتباط داشته باشن (Foreign key) میتونیم اشتراکشون رو با `INNER JOIN` بگیریم

**نکته:** عملیات `INNER JOIN` درواقع میگرده دنبال ردیف‌هایی که توی ۲ تا table داده شده برابر باشن و سپس ستون های هر دو رو ادغام میکنه و خروجی میده

یعنی اگر ما روی تیبل user و profile بیایم و `INNER JOIN` بزنیم تمام ستون‌های user و profile رو درکنار هم میده بهمون

سینتکسش به صورت زیر هست:

```sql
SELECT * FROM `table_1`
INNER JOIN `table_2` ON `table_1`.`field_1`=`table_2`.`field_2`
```

برای مثال اگر ما دنبال یوزر هایی باشیم که برای خودشون profile تشکیل دادن و اطلاعات یوزر رو به همراه اطلاعات profile اش بخوایم میتونیم روی تیبل‌های `profile` و `user` بیایم و `INNER JOIN` بزنیم

```sql title="example"
SELECT * FROM `user`
INNER JOIN `profile` ON `user`.id=`profile`.`userID`
```

خروجی کد بالا تمام ردیف‌هایی درون profile هست که profile.userID اش برابر با user.id باشه و خروجی شامل تمام ستون‌های تیبل profile و user هست

ما میتونیم بگیم کدوم ستون‌هارو میخوایم

برای مثال اگر ما دنبال تصویر کاربر هستیم و نام کاربریش میتونیم تعریف کنیم که فقط اون دوتارو بده

```sql
SELECT user.username as username, profile.image as profilePic FROM `user`
INNER JOIN `profile` ON `user`.id=`profile`.`userID`
```

توی این مثال میگرده دنبال سطرهای مشترک بین تیبل `profile` و `user` به طوری که نقطه‌ی اشتراکشون userID و id باشه که از قبل بین اونا `FOREIGN KEY` تعریف کردیم و ستون `username` رو از تیبل `user` برمیداره و اسمشو میزاره `username` و ستون `image` از تیبل `profile` رو برمیداره و اسمشو میزاره `profilePic` و این دو ستون رو برمیگردونه

### Multiple Inner Join

ما میتونیم چندین تا `INNER JOIN` هم فرابخوانیم به طوری که باید بین همه‌ی table ها اشتراک داشته باشیم و ستون‌های خروجی اجتماع تمام ستون‌های table ها هستن

```sql
SELECT * FROM `table_1`
INNER JOIN `table_2` ON `table_1`.`field_1`=`table_2`.`field_2`
INNER JOIN `table_3` ON `table_1`.`field_3`=`table_3`.`field_4`
INNER JOIN `table_4` ON `table_1`.`field_5`=`table_4`.`field_6`
...
```

ستون‌های خروجی برابر تمام ستون‌های table_1 تا table_4 درکنار هم هستن

برای مثال ما دنبال یک تراکنش هستیم که اونو با تیبل `products` و `users` بخوایم ادغام کنیم

```sql title="example"
SELECT * FROM `orders`
INNER JOIN `users` ON `user`.id=`orders`.`userID`
INNER JOIN `products` ON `products`.id=`orders`.`productdID`
```

در این مثال دنبال تمام order هایی میگرده که `userID` اش برابر آیدی یک یوزر باشه و `productID` اش برابر آیدی یک محصول باشه و اطلاعات یوزر(user) به همراه اطلاعات محصول(product) خریداری شده و اطلاعات سفارش(order) همرو کنار هم برمیگردونه.

برای مثال اگر من درمثال بالا فقط دنبال نام محصول باشم و تاریخ ثبت سفارش و یوزرنیم خریدار میتونم به شکل زیر عمل بکنم :

```sql title="example"
SELECT `users`.username as username,`orders`.date as oderDate, `products`.name as productName
FROM `orders`
INNER JOIN `users` ON `users`.id=`orders`.`userID`
INNER JOIN `products` ON `products`.id=`orders`.`productdID`
```

تو این مثال ستون‌های `username`, `orderDate`, `productsName` رو برمیگردونه

### Left, Right Join

یک متد دیگر هست برای تیبل‌هایی که باهم ارتباط دارن

- فرض کن ما دو تیبل `users` و `profiles` رو داریم. اگر از `users` روی `profile` ما `LEFT JOIN` بزنیم تمام `user` هارو برمیگردونه به همراه `profile` هایی که relation دارن
- اگر ما از `users` روی `profile` بیایم و `RIGHT JOIN` بزنیم اینبار تمام `profile` هارو برمیگردونه به همراه `user` هایی که relation دارن

درواقع توی این روش ما یکی از تیبل‌هارو به طور کامل برمیگردونیم بدون هیچ وابستگیی ولی تیبل دیگر رو فقط ردیف‌هایی رو برمیگردونیم که relation دارن

نکته: ستون‌های خروجی اجتماع تمام ستون‌های دو تیبل داده شده هستند

#### LEFT JOIN

```sql
SELECT * FROM `table_1`
LEFT JOIN `table_2` ON `table_1`.`field_1`=`table_2`.`field_2`
```

در اینجا تمام سطرهای `table_1` رو برمیگردونه و از `table_2` تنها سطر‌هایی رو برمیگردونه که `table_2.field_2 = table_1.field_1` براقرار باشه

#### RIGHT JOIN

```sql
SELECT * FROM `table_1`
RIGHT JOIN `table_2` ON `table_1`.`field_1`=`table_2`.`field_2`
```

در اینجا تمام سطرهای `table_2` رو برمیگردونه و از `table_1` تنها سطرهایی رو برمیگردونه که `table_2.field_2 = table_1.field_1` برقرار باشه

#### EXAMPLE

```sql
SELECT * FROM `users`
LEFT JOIN `profile` ON `users`.`id`=`profile`.`userID`
```

توی اینجا تمام user ها آورده میشه به همراه profile هایی که userID اشون برابر با id باشه

خروجی نهایی همچنان مانند `INNER JOIN` شامل تمام سطرهای `profiles` و `users` میشود

:::warning
درباره‌ی LEFT, RIGHT JOIN و حتی OUTER JOIN و ... خودت بیشتر مطالعه بکن

معمولا کمتر به کار میاد
:::
