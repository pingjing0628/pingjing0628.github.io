---
title: PHP 特點- 特徵機制
date: 2019-11-03 15:19:33
tags:
	- PHP
	- Traits
categories:
	- PHP
---
## 特徵機制 Traits

<!--more-->
***

* 一個特徵機制是一個局部的類別實作（像是常數、屬性和方法），他可以被**混入**一個以上的現存PHP類別，特徵機制有雙重用途：他用來形容一個類別可以做什麼（如同介面），她也提供一個模組化的實作（如同類別）

#### 為何使用特徵機制
PHP會使用類別繼承模式，但倘若想要兩個不相關的PHP類別有相似的行為呢？
特徵機制就是讓模組化實作方式可以被插入到不相關的類別中，同樣促使程式碼重用

#### 舉例兩個不相關的類別需使用同樣程式碼
* 一個PHP類別 `RetailStore`和另一個PHP類別Car是相差很大的類別，並且在繼承階層中沒有共享相同的母類別，但兩個類別都應當可以被地理編碼成經緯度以顯示在地圖上

1. 第一個解法（不良的）
建立一個共同的副類別 `Geocodable`同時讓 `RetailStore` 和 `Car`繼承，不良的方式是因為他強迫兩個不相關的類別共享相同的祖先，但這並不是原生屬於它們的繼承階層

2. 第二個解法（較好的）
建立一個 `Geocodable`介面定義了地理編碼所需要的行為方法，而 `RetailStore`和 `Car`類別可以同時實作 `Geocodable`介面，這是個好方法，因為允許每個類別保有原本的繼承階層，但是這需要我們在每個類別中重複撰寫相同的地理編碼行為，這並不是 DRY 原則的解法
> DRY 是 `Do not repeat youself`，他被認為是個良好習慣，不要再多個位置重複相同的程式碼，應當不需要因為改變一處的程式碼而改變另一處

3. 第三個解法（最好的）
建立一個 `Geocodable`特徵機制定義**並且**時做了地理編碼的行為，可以在 `RetailStore`和 `Car`類別中都混入 `Geocodable`特徵機制，而沒有污染原先的繼承階層架構

### 如何建立一個特徵機制
```php=
<?php
trait MyTrait {
	//此處是特徵機制實作內容
}
```

* `Geocodable`特徵機制定義
```php=
<?php
trait Geocodable {
	/** @var string */
	protected $address;

	/** @var \Geocoder\Geocoder */
	protected $geocoder;

	/** @var \Geocoder\Result\Geocoded */
	protected $geocoderResult;

	public function setGeocoder(\Geocoder\GeocoderInterface $geocoder)
	{
		$this->geocoder = $geocoder;
	}

	public function setAddress($address)
	{
		$this->address = $address;
	}

	public function getLatitude()
	{
		if (isset($this->geocoderResult) === false) {
			$this->geocoderAddress();
		}

		return $this->geocoderResult->getLatitude();
	}

	public function getLongitude() 
	{
		if (isset($this->geocoderResult) === false) {
			$this->geocoderAddress();
		}

		return $this->geocoderResult->getLongitude();
	}

	public function geocoderAddress()
	{
		$this->geocoderResult = $this->geocoder->geocode($this->address);

		return true;
	}
}
```