---
title: python 命名空間與範圍
date: 2019-11-22 00:11:05
tags:
	- python
	- 命名空間
categories:
	- Python
---
## Python 命名空間與範圍

<!--more-->
***

* 一個名稱可以參考不同的東西，取決於在哪裡使用它
* Python有各種的**命名空間**
* 要存取全域變數，而不是函式內的區域變數，必須明確的使用 `global`關鍵字
```python=
>>> animal = 'bat'
>>> def change_and_print_global():
        global animal
        animal = 'wombat'
        print('Inside change_and_print_global:', animal)

>>> animal
'bat'
>>> change_and_print_global: wombat
>>> animal
'wombat'
```
* 若在函式內不使用 `global`，Python會使用區域的命名空間，會在函式結束時消失
* Python提供兩個函式來存取命名空間的內容：
1. `locals()` 會回傳區域命名空間的字典內容
2. `globals()` 會回傳全域命名空間的字典內容
```python=
>>> animal = 'bat'
>>> def change_local():
        global animal
        animal = 'wombat' # local variable
        print('locals:', locals())

>>> animal
'bat'
>>> change_local()
locals: {'animal': 'bat'}
>>> print('globals:', globals())  # reformatted a little for presentation
globals: {'animal': 'bat'}...,
>>> animal
'bat'
```
* `change_local()` 內的區域命名空間只有區域變數，全域命名空間裡有另一個全域變數與一些其他東西

#### 在名稱中使用_與__
* 在Python中，名稱開頭與結尾使用兩個底線是被保留的做法，所以不能在自己的變數中使用他們