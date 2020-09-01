---
title: python 串列、Tuple、字典與集合(二)-Tuple
date: 2019-11-17 12:05:10
tags:
	- python
	- Tuple
categories:
	- Python
---
## Python 串列與tuple

<!--more-->
***

### Tuple

* Tuple與串列很像，都是一系列的任意項目，但與串列不同的事，tuple是**不可變**的，也就是說定義tuple後，就無法添加、刪除，或更改他的項目了，因此tuple很像常數串列

#### 用 `()` 建立Tuple
* 先用 `()`製作一個空的tuple：
```python=
>>> empty_tuple = ()
>>> empty_tuple
()
```
* 讓tuple有一或多個元素，在每個元素後面加上一個逗號
```python=
>>> one_marx = 'Groucho',
>>> one_marx
('Groucho',)
```
* 若有多個元素，請依序列舉，除了最後一個元素之外，都要加上一個逗號
```python=
>>> marx_table = 'Groucho', 'Chico', 'Harpo'
>>> marx_table
('Groucho', 'Chico', 'Harpo')
```
* Python 在印出tuple時，會加上括號
* 真正定義tuple的是元素後的逗號，但使用括號不會有任何不良的影響，用括號把值包起來，讓tuple看起來更清楚
```python=
>>> marx_tuple = ('Groucho', 'Chico', 'Harpo')
>>> marx_tuple
('Groucho', 'Chico', 'Harpo')
```
* tuple 可以被一次指派給多個變數
* 有時這稱為**tuple開箱(unpacking)**
```python=
>>> marx_tuple = ('Groucho', 'Chico', 'Harpo')
>>> a, b, c = marx_tuple
>>> a 
'Groucho'
>>> b
'Chico'
>>> c
'Harpo'
```

* 可以在一個陳述式中，使用tuple來交換值，不需要用到暫時性的變數
```python=
>>> password = 'swordfish'
>>> icecream = 'tuttifrutti'
>>> password, icecream = icecream, password
>>> password
'tuttifrutti'
>>> icecream
'swordfish'
```

* `tuple()`轉換函式可將其他的東西做成tuple
```python=
>>> marx_list = ['Groucho', 'Chico', 'Harpo']
>>> tuple(marx_list)
('Groucho', 'Chico', 'Harpo')
```

---
### Tuple vs 串列
* Tuple通常可以用來取代串列，但他們的功能比較少，沒有 `append()`、`insert()`，為什麼不使用串列來取代所有的tuple就好？
1. tuples佔用的空間較少
2. 不會不小心破壞tuple的項目
3. 可以將tuple當成字典鍵
4. 可以將**具名tuple(Named tuple)**當成物件的簡化替代品
5. 函式引數是以tuple的形式傳入的