---
title: python 產生器
date: 2019-11-21 23:21:25
tags:
	- python
	- 產生器
categories:
	- Python
---
## Python 產生器

<!--more-->
***


* 產生器generator是一種Python序列建立物件
* 可以迭代很大的序列，而不需要在記憶體中一次建立或儲存整個序列
* ex: `range()`
* 每次迭代產生器時，都會記得上一次被呼叫時的所在的位置，並回傳下一個值
* 一般函式不會記得之前的呼叫，且永遠會從他的第一行，以相同的狀態開始

* 若想建立一個可能會很大的序列，且程式很大，無法使用產生器生成式，可以編寫**產生器函式**，他是一種一般的函式，但會用 `yield`陳述式來回傳值，而不是 `return`
```python=
>>> def my_range(first=0, last=10, step=1):
        number = first
        while number < last:
        yield number
        number += step
// 是一個普通的函式
>>> my_range
<function my_range at 0x10193e268>

// 回傳一個產生器物件
>>> ranger = my_range(1, 5)
>>> ranger
<generator object my_range at 0x10193e168>

// 迭代這個產生器物件
>>> for x in ranger:
        print(x)
1
2
3
4
```