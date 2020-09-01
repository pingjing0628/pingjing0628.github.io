---
title: PHP 特點- 閉包
date: 2019-11-03 15:23:13
tags:
	- PHP
	- closure
categories:
	- PHP
---
## 閉包

<!--more-->
***

* 閉包是在創造時就封裝了內部狀態的函式，即使原有的環境已經消失了這個被封裝的狀態會中被寶存在閉包中
* Example `array_map`閉包
```php=
	<?php 
	$numbersPlusOne = array_map(function ($number) {
		return $number + 1;
	}, [1,2,3]);
	print_r($numbersPlusOne);
	//輸出 --> [2,3,4]
```
* PHP閉包是一種物件，每一閉包實體都有其內部狀態，就跟其他PHP物件一樣可以用 `$this` 關鍵字存取

#### 繫結狀態

* Example 利用 `use`關鍵字繫結狀態到閉包
```php=
	<?php 
	function enclosePerson($name) {
		return function ($doCommand) use ($name) {
			return sprintf('%s, %s', $name, $doCommand);
		};
	}

	// 將"Clay"關閉在閉包中
	$clay = enclosePerson('Clay');

	// 使用指令呼叫閉包
	echo $clay('get me sweet tea!');
	// 輸出 --> "Clay, get me sweet tea!"
```