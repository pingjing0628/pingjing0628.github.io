---
title: PHP 良好習慣- 消毒、驗證和逃脫
date: 2019-11-03 16:28:11
tags:
	- PHP
categories:
	- PHP
---
## 消毒

<!--more-->
***

### 別相信任何人

* $_GET
* $_POST
* $_REQUEST
* $_COOKIE
* $argv
* php://stdin
* php://input
* file_get_contents()
* 遠端資料庫
* 遠端API
* 來自客戶端的資料

suggestions: **消毒**使用者輸入, **驗證**資料, **逃脫**使用者輸出

### 消毒使用者輸入
* 消除不安全的字元，在資料到達應用程式存儲層**之前**就消毒
* 假設你的網頁允許用HTML下評論，原始設定中，沒有任何東西會阻止使用者在評論中使用不合理的 `<script>`標籤
* 會遭遇到以下幾種類別的輸入資料：HTML、SQL查詢、使用者個人資料

#### HTML
* 利用 `htmlentities()`函式，將HTML的特殊字元消毒成對應的HTML代表字元

#### SQL查詢
* 有些情況會以使用者輸入的資料來建立SQL查詢，有時這些資料由HTTP請求的查詢字串而來(例如 `?user = 1`)，有時由HTTP請求的URI片段而來(ex: `/user/1`)

#### 使用個人資料
* PHP提供 `filter_var()`和 `filter_input()`函式
* 這兩個函式接受不同的旗標來消毒不同形式的使用者輸入：電子郵件、URL編碼字串、整數、浮點數、HTML字元、URL和指定的ASCII字元範圍

### 驗證資料
* 驗證與消毒不同，驗證並沒有把輸入資料中的資訊去除，驗證只有確認輸入資料有達到你的預期，如果你預期一個電子郵件位址，確認這個輸入資料真的是個電子郵件位址

### 逃脫使用者輸出
* 當要把資料輸出到網頁或是API回應時，逃脫你的輸出是重要的，這讓應用程式多增加了一層防護，防止惡意程式被輸出或是被使用者執行

### 密碼雜湊API
* 使用者註冊腳本
```php=
<?php 
try {
	// 驗證電子郵件
	$email = filter_input(INPUT_POST, 'email', FILTER_VALIDATE_EMAIL);
	if (! $email) {
		throw new Exception('Invalid email');
	}

	// 驗證密碼
	$password = filter_input(INPUT_POST, 'password');
	if (! $password || mb_strlen($password) < 8) {
		throw new Exception('Password must contain 8+ characters');
	}

	// 建立密碼雜湊值
	$passwordHash = password_hash(
		$password,
		PASSWORD_DEFAULT,
		['cost' => 12]
	);
	if ($passwordHash === false) {
		throw new Exception('Password hash failed');
	}

	// 建立使用者帳戶（這是虛擬碼）
	$user = new User();
	$user->email = $email;
	$user->password_hash = $passwordHash;
	$user->save();

	// 重新導向到登入頁面
	header('HTTP/1.1 302 Bad Redirect');
	header('Location: /login.php');
} catch (Exception $e) {
	// 回報錯誤
	header('HTTP/1.1 400 Bad request');
	echo $e->getMessage();
}
```