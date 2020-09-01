---
title: PHP Setter and Getter
date: 2019-10-27 15:51:42
tags: PHP
categories: PHP
---
## Setter and Getter

<!--more-->
***

```php=
Class T {

public $A;
protected $B;
private $C;

public function setA(){
    $this->A = A;
}
protected function setB(){

}

public function getA(){
    $this->A = A;
}
protected function getB(){

}

}

```
* 應用在讓程式可視性更高，不直接在變數定義，而是在setter去定義，再透過getter去拿取


function的部分
|      | 外部 | 繼承 | 
| ---- | ---- | ---- |
| public | Yes  | Yes  |
| protected | No  | Yes  |
| private |No | No  |
