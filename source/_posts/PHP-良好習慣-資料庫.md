---
title: PHP 良好習慣- 資料庫
date: 2019-11-03 17:45:56
tags:
	- PHP
	- Database
categories:
	- PHP
---
## Database

<!--more-->
***

### PDO 擴充
* PHP 提供了原生的PDO擴充，PDO(PHP data objects)是一組PHP類別，可以用單一使用者介面和許多不同的SQL資料庫溝通，讓資料庫的實作方式被抽象化出來，無論使用哪種資料庫系統，都可以用單一的介面撰寫和執行資料庫查詢

* PDO 建構子
```php=
<?php
try {
	$pdo = new PDO(
		'mysql:host=127.0.0.1;dbname=books;port=3306;charset=utf8',
		'USERNAME',
		'PASSWORD'
	);
} catch (PDOException $e) {
	// 資料庫連線失敗
	echo "Database connection failed";
	exit;
}
```