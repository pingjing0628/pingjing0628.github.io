---
title: python 裝飾器
date: 2019-11-21 23:36:17
tags:
	- python
	- 裝飾器
categories:
	- Python
---
## Python 裝飾器

<!--more-->
***


* 有時會修改既有的函式，不想更改原始程式碼，常見的例子，就是加入一個除錯的陳述式，來查看有哪些引數被傳入
* **裝飾器**decorator 是一種函式，他會接收一個函式，並回傳另一個函式
1. `*args` & `**kwargs`
2. 內部函式
3. 當成引數的函式

* `document_it()` 函式會定義一個裝飾器，他會做以下的工作：
1. 印出函式的名稱，與引數的值
2. 用引數來執行函式
3. 印出結果
4. 回傳修改後的函式，以供使用

```python=
>>> def document_it(func):
        def new_function(*args, **kwargs):
            print('Running function:', func.__name__)
            print('Positional arguments:', args)
            print('Keyword arguments:', kwargs)
            result = func(*args, **kwargs)
            print('Result:', result)
            return result
        return new_function
```

* 無論將什麼 `func`傳給 `document_it()`，都可以得到一個新的函式，裡面有 `document_it()`加入的陳述式
* 裝飾器不一定要執行任何 `func`的程式，但 `document_it()`會在過程中呼叫 `func`，讓你可以取得 `func`的結果及所有額外的東西
```python=
>>> def add_ints(a, b):
        return a + b
>>> add_ints(3, 5)
8
>>> cooler_add_ints = document_it(add_ints) #manual decorator assignment
>>> cooler_add_ints(3, 5)
Running function: add_ints
Positional arguments: (3, 5)
Keyword arguments: {}
Result: 8
8
```

* 也可以在想要裝飾器的函式前添加 **@decorator_name**，來取代上述的手動指派裝飾器
```python=
>>> @document_it
    def add_ints(a, b):
        return a + b

>>> add_ints(3, 5)
Running function add_ints
Positional arguments: (3, 5)
Keyword arguments: {}
Result: 8
8
```

* 同一個函式可以有兩個以上的裝飾器
```python=
>>> def square_it(func):
        def new_function(*args, **kwargs):
            result = func(*args, **kwargs)
            return result * result
        return new_function
```

* 最靠近函式的裝飾器，(在def正上方)，會先執行，無論順序為何，最終的結果都一樣的，但過程中有不同的步驟
```python=
>>> @document_it
    @square_it
    def add_ints(a, b)
        return a + b

>>> add_ints(3, 5)
Running function new_function
Positional arguments: (3, 5)
Keyword arguments: {}
Result: 64
64
```
* 改變裝飾器的順序
```python=
>>> @square_it
    @document_it    
    def add_ints(a, b)
        return a + b

>>> add_ints(3, 5)
Running function new_function
Positional arguments: (3, 5)
Keyword arguments: {}
Result: 8
64
```