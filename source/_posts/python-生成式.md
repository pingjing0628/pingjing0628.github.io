---
title: python 生成式
date: 2019-11-17 23:15:34
tags:
	- python
	- 生成式
categories:
	- Python
---
## Python 生成式

<!--more-->
***

### 串列生成式
* 你可以建立一個從整數1到5的串列，一次建立一個項目
```python=
>>> number_list = []
>>> number_list.append(1)
>>> number_list.append(2)
>>> number_list.append(3)
>>> number_list.append(4)
>>> number_list.append(5)
>>> number_list
[1, 2, 3, 4, 5]
```
* 你也可以使用迭代器與 `range()`函式
```python=
>>> number_list = []
>>> for number in range(1, 6):
        number_list.append(number)

>>> number_list
[1, 2, 3, 4, 5]
```
* 你也可以直接將 `range()`的輸出轉成串列：
```python=
>>> number_list = list(range(1, 6))
>>> number_list
[1, 2, 3, 4, 5]
```

* 以上所有方法都是有效的Python程式，也都會產生相同的結果，但要建立較符合Python風格的方式，就是使用**串列生成式**，最簡單的串列生成式形式是：

> [運算式 for 項目 in 可迭代項目]

* 以下是用串列生成式建構整數串列的方式
```python=
>>> number_list = [number for number in range(1, 6)]
>>> number_list
[1, 2, 3, 4, 5]
```
* 如上，第一個number變數來產生串列的值，將迴圈的結果放入number_list
* 第二個 number是 for迴圈的一部份，為了說明第一個number是個運算式，範例如下：
```python=
>>> number_list = [number-1 for number in range(1, 6)]
>>> number_list
[0, 1, 2, 3, 4]
```

* 串列生成式可以容納條件運算式

> [運算式 for 項目 in 可迭代項目 if 條件式]

* 製作一個新的生成式，用來建立一個裡面只有1到5的奇數的串列
```python=
>>> a_list = [number for number in range(1, 6) if number % 2 == 1]
>>> a_list
[1, 3, 5]
```
* 傳統的
```python=
>>> a_list = []
>>> for number in range(1, 6):
        if number % 2 == 1:
            a_list.append(number)
>>> a_list
[1, 3, 5]
```

* 最後，也可以在對應的生成式中使用二組以上的for子句
* 舊式的嵌套迴圈
```python=
>>> rows = range(1,4)
>>> cols = range(1,3)
>>> for row in rows:
	    for col in cols:
	        print(row, col)
1 1
1 2
2 1
2 2
3 1
3 2  
```
* 再來，使用生成式，並將他指派給變數cells，製作一串(row, col)tuple：
```python=
>>> rows = range(1,4)
>>> cols = range(1,3)
>>> cells = [(row, col) for row in rows for col in cols]
>>> for cell in cells
        print(cell)
(1, 1)
(1, 2)
(2, 1)
(2, 2)
(3, 1)
(3, 2) 
```