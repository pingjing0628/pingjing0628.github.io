---
title: PHP 特點- 名稱空間&介面
date: 2019-11-03 15:16:41
tags:
	- PHP
	- Namespaces
	- Interface
categories:
	- PHP
---
## 名稱空間 Namespaces

<!--more-->
***

* 現代PHP特點之一
* 創造出程式碼並獨立運作於其他開發者的程式
* 缺乏Namespace，若發生名稱的重複會導致PHP運作失敗

## 程式碼作為介面

* 介面是一個介於兩個PHP物件之間的合約，讓一個物件了解另一個物件**可以做什麼**，而非另一個物件**是什麼**
* Example:
一個PHP類別為 `DocumentStore` ，他用來搜集來自不同來源的文字
 **DocumentStore類別定義**
```php=
class DocumentStore
{
	protected $data = [];

	public function addDocument(Documentable $document)
	{
		$key = $document->getId();
		$value = $document->getContent();
		$this->data[$key] = $value;
	}

	public function getDocuments()
	{
		return $this->data;
	}
}
```
如果addDocument()方法只接受 `Documentable` 類別的實例，那這段程式碼要如何運作？但 `Documentable` 並不是一個類別而是一個介面，如下：
 **Documentable介面定義**
```php=
interface Documentable
{
	public function getId();

	public function getContent();
}
```
這個介面定義說明了任何實作 `Documentable` 介面的物件都必須提供一個公開(public)的 `getId()` 方法和一個公開(public)的 `getContent()` 方法
1. 以下示範一個**從遠端URL用curl擷取HTML的實作方式**
```php=
class HTMLDocument implements Documentable
{
	protected $url;

	public function __construct($url)
	{
		$this->url = $url;
	}

	public function getId()
	{
		return $this->url;
	}

	public function getContent()
	{
		$ch = curl_init();
		curl_setopt($ch, CURLOPT_URL, $this->url);
		curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
		curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 3);
		curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);
		curl_setopt($ch, CURLOPT_MAXREDIRS, 3);
		$html = curl_exec($ch);
		curl_close($ch);

		return $html;
	}
}
```
2. 擷取終端機指令的結果
```php=
class CommandOutputDocument implements Documentable
{
	protected $command;

	public function __construct($command)
	{
		$this->command = $command;
	}

	public function getId()
	{
		return $this->command;
	}

	public function getContent()
	{
		return shell_exec($this->command);
	}
}
```
以下示範如何用 `DocumentStore` 類別配合上述文件收集實作方式
```php=
<?php
$documentStore = new DocumentStore;

// 加入HTML文件
$htmlDoc = new HTMLDocument('http://php.net');
$documentStore->addDocument($htmlDoc);

// 加入終端機指令文件
$cmdDoc = new CommandOutputDocument('cat /etc/hosts');
$documentStore->addDocument($cmdDoc);

print_r($documentStore->getDocuments());
```
* 介面創造了更多彈性的程式碼，讓其他人可以專注在實作的細節考量上．
