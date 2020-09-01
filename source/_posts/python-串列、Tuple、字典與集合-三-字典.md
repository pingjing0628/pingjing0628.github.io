---
title: python 串列、Tuple、字典與集合(三)-字典
date: 2019-11-17 15:08:50
tags:
	- python
	- 字典
categories:
	- Python
---
## Python 字典

<!--more-->
***


### 字典(Dictionary)
* 字典與串列很像，但他不在乎項目的順序，且不會用0或1等位移值還選擇項目
* 必須為每一個值指定一個獨一無二的**鍵**
* 這個鍵通常是個字串，但其實可以用任何一種不可變的Python類型：布林、整數、浮點數、tuple、字串...
* 字典通常是可變的，可以添加、刪除、改變他們的 "鍵/值"元素

>其他語言可能會將字典稱為**關聯陣列(associative array)、雜湊(Hash)或 hashmap**，在Python，字典也可稱為dict還減少字母數 

#### 用 `{}`來建立
* 要建立字典，要用大括號包住以逗號分隔的**鍵**
```python=
>>> empty_dict = {}
>>> empty_dict
{}
```

#### 用`dict()`來轉換
* 可以使用`dict()`，將雙值序列轉換到字典裡面，每一個序列的第一個項目都會被當成鍵，第二個項目會被當成值
```python=
>>> lol = [['a', 'b'], ['c', 'd'], ['e', 'f']]
>>> dict(lol)
{'c': 'd', 'a': 'b', 'e' :'f'}
```

> 字典裡的鍵的順序都是任意排列的

* 可以使用任何存放雙項目序列的序列
1. 一個含有雙項目tuple的串列
```python=
>>> lot = [('a', 'b'), ('c', 'd'), ('e', 'f')]
>>> dict(lot)
{'c': 'd', 'a': 'b', 'e' :'f'}
```

2. 一個含有雙項目串列的tuple
```python=
>>> tol = (['a', 'b'], ['c', 'd'], ['e', 'f'])
>>> dict(tol)
{'c': 'd', 'a': 'b', 'e' :'f'}
```

3. 一個含有雙字元字串的串列
```python=
>>> los = ['ab', 'cd', 'ef']
>>> dict(los)
{'c': 'd', 'a': 'b', 'e' :'f'}
```

4. 一個含有雙字元字串的tuple
```python=
>>> tos = ('ab', 'cd', 'ef')
>>> dict(tos)
{'c': 'd', 'a': 'b', 'e' :'f'}
```

#### 用[鍵]來添加或變更項目
* 用項目的鍵來參考並指派值，就可以將一個項目加到字典
* 若字典裡已經有此鍵，則既有的值就會被換成新的值
* 若鍵是新的，則會與值一起被加到字典裡
* 製作一個字典，且姓為鍵，名為值
```python=
>>> pythons = {
	'Chapmon': 'Graham',
	'Cleese': 'John',
	'Idle': 'Eric',
	'Jones': 'Terry',
	'Palin': 'Michael',
}
>>> pythons
{'Cleese': 'John', 'Jones': 'Terry', 'Palin': 'Michael', 'Chapmon': 'Graham', 'Idle': 'Eric'}
```

* 少加入一位成員
```python=
>>> pythons['Gilliam'] = 'Gerry'
>>> pythons
{'Cleese': 'John', 'Gilliam': 'Gerry', 'Jones': 'Terry', 'Palin': 'Michael', 'Chapmon': 'Graham', 'Idle': 'Eric'}
```

* 但是打錯名字了，藉由使用同一個鍵，去修正
```python=
>>> pythons['Gilliam'] = 'Terry'
>>> pythons
{'Cleese': 'John', 'Gilliam': 'Terry', 'Jones': 'Terry', 'Palin': 'Michael', 'Chapmon': 'Graham', 'Idle': 'Eric'}
```

#### 用 `update()`來合併字典
* 使用 `update()`將一個字典的鍵與值複製到另一個
```python=
>>> pythons = {
	'Chapmon': 'Graham',
	'Cleese': 'John',
	'Gilliam': 'Terry'
	'Idle': 'Eric',
	'Jones': 'Terry',
	'Palin': 'Michael',
}
>>> pythons
{'Cleese': 'John', 'Gilliam': 'Terry', 'Jones': 'Terry', 'Palin': 'Michael', 'Chapmon': 'Graham', 'Idle': 'Eric'}
>>> others = {'Marx': 'Groucho', 'Howard': 'Moe'}
>>> pythons.update(others)
>>> pythons
{'Cleese': 'John', 'Howard': 'Moe', 'Gilliam': 'Terry', 'Jones': 'Terry', 'Palin': 'Michael', 'Marx': 'Groucho', 'Chapmon': 'Graham', 'Idle': 'Eric'}
```

* 若第二個字典與要合併的字典有相同的鍵時，第二個字典的值會勝出
```python=
>>> first = {'a': 1, 'b': 2}
>>> second = {'b': 'platypus'}
>>> first.update(second)
>>> first
{'b': 'platpyus', 'a': 1}
```

#### 用 `del`與鍵來刪除項目
```python=
>>> del pythons['Marx']
>>> pythons
{'Cleese': 'John', 'Howard': 'Moe', 'Gilliam': 'Terry', 'Jones': 'Terry', 'Palin': 'Michael', 'Chapmon': 'Graham', 'Idle': 'Eric'}
>>> del pythons['Howard']
>>> pythons
{'Cleese': 'John', 'Gilliam': 'Terry', 'Jones': 'Terry', 'Palin': 'Michael', 'Chapmon': 'Graham', 'Idle': 'Eric'}
```

#### 用 `clear()`來刪除所有項目
* 可以使用 `clear()`或重新指派一個空的字典{}
```python=
>>> pythons.clear()
>>> pythons
{}
>>> pythons = {}
>>> pythons
{}
```

#### 用 `in`來測試鍵
```python=
>>> pythons = {
	'Chapmon': 'Graham',
	'Cleese': 'John',
	'Jones': 'Terry',
	'Palin': 'Michael',
}
>>> 'Chapman' in pythons
True
>>> 'Palin' in pythons
True
>>> 'Gilliam' in pythons
False
```

#### 用[鍵]來取得一個項目
```python=
>>> pythons['Cleese']
John
```

* 若不存在於字典，則會得到例外
```python=
>>> pythons['Marx']
Traceback (most recent call last):
	File "stdin", line 1, in <module>
KeyError: 'Marx'
```

* 避免這件事的兩種方式，一是使用 `in`測試鍵
```python=
>>> 'Marx' n pythons
False
```

* 二是使用特殊的字典函式 `get()`，要提供字典、鍵、及一個選用的值，若鍵存在，則可以取得他的值
```python=
>>> python.get('Cleese')
'John'
```

* 若不存在，則會取得選用的值(若有指定的話)
```python=
>>> pythons.get('Marx', 'Not a python')
'Not a python'
```
* 否則會得到None
```python=
>>> pythons.get('Marx')
>>>
```

#### 使用 `keys()`來取得所有的鍵
```python=
>>> signals = {
	'green': 'go',
	'yellow': 'go faster',
	'red': 'smile for the camera'
}
>>> signals.keys()
dict_keys(['green', 'red', 'yellow'])
```

>在Python 2 `keys()`會回傳一個串列，Python 3 會回傳 `dict_keys()`，他是可迭代得鍵資料，適合用在大型的字典，不會浪費時間與記憶體來建立及儲存可能用不到的串列，但通常你其實**想要**一個串列
在Python 3 ，必須呼叫 `list()`來將`dict_keys()`物件轉換成串列
```python=
>>> list(signals.keys())
['green', 'red', 'yellow']
```
在Python 3 ，也需要使用 `list()`函式來將 `values()`與 `items()`的結果轉成一般的Python串列

#### 用 `values()`來取得所有的值
```python=
>>> list(signals.values())
['go', 'go faster', 'smile for the camera']
```

#### 用 `items()`來取得所有的鍵/值對
* 每一個鍵與值都會以tuple的形式回傳
```python=
>>> list(signals.items())
[('green', 'go'), ('red', 'go faster'), ('yellow', 'smile for the camera')]
```

#### 用 `=`來指派，用 `copy()`來複製
```python=
>>> signals = {
	'green': 'go',
	'yellow': 'go faster',
	'red': 'smile for the camera'
}
>>> save_signals = signals
>>> signals['blue'] = 'confuse everyone'
>>> save_signals
{'blue': 'confuse everyone', 'green': 'go', 'yellow': 'go faster', 'red': 'smile for the camera'}
```

* 要將某個字典內的鍵與值複製到其他字典來避免這種情形，使用 `copy()`
```python=
>>> signals = {
	'green': 'go',
	'yellow': 'go faster',
	'red': 'smile for the camera'
}
>>> original_signals = signals.copy()
>>> signals['blue'] = 'confuse everyone'
>>> save_signals
{'blue': 'confuse everyone', 'green': 'go', 'yellow': 'go faster', 'red': 'smile for the camera'}
>>> original_signals
{'green': 'go', 'yellow': 'go faster', 'red': 'smile for the camera'}
```