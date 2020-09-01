---
title: python 串列、Tuple、字典與集合(四)-集合
date: 2019-11-17 16:32:16
tags:
	- python
	- 集合
categories:
	- Python
---
## Python 集合

<!--more-->
***

### 集合(Set)

* 集合就像是被移除值，只留下鍵的字典，與字典一樣，每個鍵都是獨一無二的
* 當只想要知道某個東西是否存在，其他都無所謂時，就是使用集合的時機
* 若是想要將某些資訊指派給鍵作為它的值，就使用字典

#### 以`set()`來建立
```python=
>>> empty_set = set()
>>> empty_set
set()
>>> even_numbers = {0, 2, 4, 6, 8}
>>> even_numbers
{0, 8, 2, 4, 6}
>>> odd_numbers = {1, 3, 5, 7, 9}
>>> odd_numbers
{9, 3, 1, 5, 7}
```
* 與字典一樣，集合是無序的

> 因為[]建立一個空串列，或許認為{}也會建立一個空集合
其實，{}會建立一個空字典，這就是解譯器將空集合印成set()，而不是{}的原因
因為在Python中，字典可優先取用大括號

#### 用 `set()`轉換成其他類型
* 你可能會用串列、字串、tuple或字典來建立集合，並丟棄任何重複的值
* 一個字串裡面可能有重複的字母：
```python=
>>> set('letters')
{'l', 'e', 't', 'r', 's'}
```
* 這個集合裡面只有一個'e'和't'

* 接著將串列變成集合
```python=
>>> set(['Dasher', 'Dancer', 'Prancer', 'Mason-Dixon'])
{'Dancer', 'Dasher', 'Prancer', 'Mason-Dixon'}
```

* tuple 變集合
```python=
>>> set(('Ummagumma', 'Echoes', 'Atom Heart Mother'))
{'Ummagumma', 'Atom Heart Mother', 'Echoes'}
```

* 字典丟給 `set()`，他只會用到鍵
```python=
>>> set({'apple': 'red', 'orange': 'orange', 'cherry': 'red'})
{'apple', 'cherry, 'orange'}
```

#### 使用 `in`來測試值
* 這是集合最常用到的地方，先建立一個稱為drinks 的字典，其中，每一個鍵都是一種混合飲料的名稱，他對應的值是他的材料集合：
```python=
>>> drinks = {
	'martini': {'vodka', 'vermouth'}, 
	'black russian': {'vodka', 'kahlua'},
	'white russian': {'cream', 'kahlua', 'vodka'},
	'manhattan': {'rye', 'vermouth', 'bitters'},
	'screwdriver': {'orange juice', 'vodka'}
}
```
* 集合都是用大括號包起來的，但他們只是一系列的值，字典是一或多個**鍵：值**對
* 哪些飲料有vodka？
```python=
>>> for name, contents in drinks.items():
		if 'vodka' in contents:
			print(name)

screwdriver
martini
black russian
white russian
```
* 想喝有vodka的飲料，但有乳糖不耐症，且覺得vermouth喝起來像煤油：
```python=
>>> for name, contents in drinks.items():
		if 'vodka' in contents and not ('vermouth' in contents or 'cream' in contents):
			print(name)

screwdriver
black russian
```

#### 結合與運算子
* 使用**集合交集運算子**，也就是 & 符號
```python=
>>> for name, contents in drinks.items():
		if contents & {'vermouth', 'orange juice'}:
			print(name)

screwdriver
martini
manhattan
```
* & 運算子會產生一個集合，裡面存有你所比較的兩個串列內都有的項目，如果contents裡面沒有任何那些材料，&會回傳一個空集合，他會被視為false
* 接著來改寫上面的範例
```python=
>>> for name, contents in drinks.items():
		if 'vodka' in contents and not contents & {'vermouth', 'cream'}:
			print(name)

screwdriver
black russian
```
* 將這兩個飲料的材料集合存在變數內，節省之後範例的打字次數
```python=
>>> bruss = drinks['black russian']
>>> wruss = drinks['white russian']
```

* 以下是使用集合運算子的範例
```python=
>>> a = {1, 2}
>>> b = {2, 3}
```
* 用&符號或集合的 `intersection()`來取得**交集**
```python=
>>> a & b
{2}
>>> a.intersection(b)
{2}
```
```python=
>>> bruss & wruss
{'kahlua', 'vodka'}
```
* 使用 | 或 `union()`集合函式來取得**聯集**(兩組集合的成員)
```python=
>>> a | b
{1, 2, 3}
>>> a.union(b)
{1, 2, 3}
```
```python=
>>> bruss | wruss
{'cream', 'kahlua', 'vodka'}
```

* 差集(屬於第一個集合，但不屬於第二個集合的成員)的取得方式，是使用 `-`字元，或 `difference()`
```python=
>>> a - b
{1}
>>> a.difference(b)
{1}

>>> bruss - wruss
set()
>>> wruss - bruss
{'cream'}
```

* **互斥或**(只屬於其中一個集合的項目)使用 ^ 或 `symmetric_difference()`：
```python=
>>> a ^ b
{1, 3}
>>> a.symmetric_difference(b)
{1, 3}

>>> bruss ^ wruss
{'cream'}
```

* `<=`或 `issubset()`檢查某個集合是否為另一個集合的**子集合**(第一個集合的所有成員都屬於第二個集合)：
```python=
>>> a <= b
False
>>> a.issubset(b)
False

>>> bruss <= wruss
True

>>> a <= a
True
>>> a.issubset(a)
True
```

* 要讓**真子集(proper subset)**成立，第二個集合必須擁有第一個集合的所有成員，此外還要有其他的成員
```python=
>>> a < b
False
>>> a < a
False

>>> bruss < wruss
True
```

* **超集合(superset)**是子集合的相反(第二個集合的所有成員都是第一個集合的成員)
```python=
>>> a >= b
False
>>> a.issuperset(b)
False

>>> wruss >= bruss
True
```
* 所有集合都是自己的集合
```python=
>>> a >= a
True
>>> a.issuperset(a)
True
```
* 使用 `>` 來找出**真超集合(proper superset)**
```python=
>>> a > b
False
>>> wruss > bruss
True
```
* 集合無法成為自己的真超集合：
```python=
>>> a > a
False
```

#### 比較資料結構
* 使用方括號[]製作串列
* 用逗號製作tuple
* 用大括號製作字典{}
```python=
>>> marx_list = ['Groucho', 'Chico', 'Harpo']
>>> marx_tuple = 'Groucho', 'Chico', 'Harpo'
>>> marx_dict = {'Groucho': 'banjo', 'Chico': 'piano', 'Harpo': 'harp'}
>>> marx_list[2]
'Harpo'
>>> marx_tuple[2]
'Harpo'
>>> marx_dict['Harpo']
'harp'
```
* 對串列與tuple，方括號內的值是整數位移值
* 對字典而言，它是鍵

#### 製作更大的資料結構
* 從三個不同的串列開始：
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> pythons = ['Chapman', 'Cleese', 'Gilliam', 'Jones', 'Palin']
>>> stooges = ['Moe', 'Curly', 'Larry']
```

* 製作一個tuple，將每個串列當成元素：
```python=
>>> tuple_of_lists = marxes, pythons, stooges
>>> tuple_of_lists
(['Groucho', 'Chico', 'Harpo'], ['Chapman', 'Cleese', 'Gilliam', 'Jones', 'Palin'], ['Moe', 'Curly', 'Larry'])
```

* 也可製作一個含有這三個串列的串列：
```python=
>>> list_of_lists = [marxes, pythons, stooges]
>>> list_of_lists
[['Groucho', 'Chico', 'Harpo'], ['Chapman', 'Cleese', 'Gilliam', 'Jones', 'Palin'], ['Moe', 'Curly', 'Larry']]
```

* 製作字典
```python=
>>> dict_of_lists = {'Marxes': marxes, 'Pythons': pythons, 'Stooges': stooges}
>>> dict_of_lists
{'Stooges': ['Moe', 'Curly', 'Larry'], 'Marxes': ['Groucho', 'Chico', 'Harpo'], 'Pythons': ['Chapman', 'Cleese', 'Gilliam', 'Jones', 'Palin']}
```

* 唯一的限制就是這些資料類型本身，如：字典鍵是不可變的，所以不能將串列、字典或集合當成其他字典的鍵，但是tuple可以，例如：可以用GPS座標來指引位置：
```python=
>>> houses = {(44.79, -93.14, 285): 'My House', (38.89, -77.03, 13): 'The White House'}
```