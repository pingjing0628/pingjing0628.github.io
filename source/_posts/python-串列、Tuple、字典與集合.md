---
title: python 串列、Tuple、字典與集合(一)-串列
date: 2019-11-16 00:18:31
tags:
	- python
	- 串列
categories:
	- Python
---
## Python 串列與tuple

<!--more-->
***

* Python有兩種序列結構：**tuple** 與 **串列(list)**
* 他們都有零個或多個元素，與字串不同的是，可以用不同類型的元素
* 每一個元素都可以是**任何Python**物件，因此可以任意地創造有深度且複雜的結構

#### Tuple vs 串列
* Tuple是不可變的，當指派一個元素給tuple後，他就再也不能更改了
* 串列是可變的，可以盡情地插入與刪除元素

---
### 串列
* 串列適合用來追蹤東西的順序，特別是順序與內容可能會改變的時候
* 在串列中，同一個值可能會出現一次以上

#### 用 [] 或 list() 建立
* 串列由0或多個元素組成的，以逗號分隔
```python=
>>> empty_list = []
>>> weekdays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday']
>>> big_birds = ['emu', 'ostrich', 'cassowary']
>>> first_names = ['Graham', 'John', 'Terry', 'Michael']
```
* 也可以用 `list()`函式製作一個空串列
```python=
>>> another_empty_list = list()
>>> another_empty_list
[]
```

#### 用list()來將其它的資料類型轉換成串列
* example
```python=
>>> list('cat')
['c', 'a', 't']
```

* 範例：將一個 `tuple`轉換成一個串列
```python=
>>> a_tuple = ('ready', 'fire', 'aim')
>>> list(a_tuple)
['ready', 'fire', 'aim']
```

* 用 `spilt()`來分割
```python=
>>> birthday = '1/6/1952'
>>> birthday.split('/')
['1', '6', '1952']
```
* 如上，若有連續兩個以上的分隔字串，則會得到一個空字串的串列項目：
```python=
>>> splitme = 'a/b//c/d///e'
>>> splitme.split('/')
['a', 'b', '', 'c', 'd', '', '', 'e']
```
* 若改用雙字元分隔字串 //
```python=
>>> splitme = 'a/b//c/d///e'
>>> splitme.split('//')
>>>
['a/b', 'c/d', '/e']
```

#### 使用[位移值]來取得一個項目
* 如同字串，可以藉由指定串列內的某個值的位移值來取出
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> marxes[0]
'Groucho'
>>> marxes[1]
'Chico'
>>> marxes[2]
'Harpo'
```

#### 使用[位移值]來更改一個項目
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> marxes[2] = 'Wanda'
>>> marxes
['Groucho', 'Chico', 'Wanda']
```

#### 用一個範圍的位移值來以`slice` 取出項目
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> marxes[0:2]
['Groucho', 'Chico']
```
* 串列的`slice`仍然是一個串列
* 以下有一些範例
1. 以2的間隔值往右移動
```python=
>>> marxes[::2]
['Groucho', 'Harpo']
```
2. 以2的間隔值往左移動
```python=
>>> marxes[::-2]
['Harpo', 'Groucho']
```
3. 將串列反過來的技巧
```python=
>>> marxes[::-1]
['Harpo', 'Chico', 'Groucho']
```

#### 用 `append()`將項目附加到結尾
```python=
>>> marxes.append('Zeppo')
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo']
```

#### 用 `extend()`或 += 來結合串列
* 使用 `extend()`將一個串列合併到另一個串列
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> others = ['Gummo', 'Karl']
>>> marxes.extend(others)
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo', 'Gummo', 'Karl']
```

* 使用 `append()`，others會被加成一個串列項目，而不是合併裡面的項目
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> others = ['Gummo', 'Karl']
>>> marxes.append(others)
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo', ['Gummo', 'Karl']]
```

#### 用 `insert()` 與位移值來加入一個項目
* `append()`只能將項目加到串列的結尾，當欲將一個項目加到串列任何一個位移值之前時，可以使用`insert()`
* 超出串列結尾的位移值就會將項目加至結尾
```python=
>>> marxes.insert(3, 'Gummo')
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo']
>>> marxes.insert(10, 'Karl')
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo', 'Karl']
```

#### 用 `del`與位移值來刪除一個項目
* 使用串列位置來刪除一筆項目，他後面的項目會往前遞補到被刪除的項目的位置，且串列長度減一
```python=
>>> del marxes[-1]
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo']
```

* 刪除最後一個marxes串列版本的'Harpo'
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo']
>>> marxes[2]
'Harpo'
>>> del marxes[2]
>>> marxes
['Groucho', 'Chico', 'Gummo', 'Zeppo']
>>> marxes[2]
'Gummo'
```

> del 是 Python 陳述式，不是串列方法，就是不能說 marxes[-2].del()．他是一種賦值(=)的反向動作：他會將Python物件與名稱分離，而且如果那個名稱是最後一個參考物件的名稱，也會釋出物件的記憶體

#### 用 `remove()`與值來刪除項目
* 若是不確定或不在乎某個項目在串列中的哪個位置，使用 `remove()` 與他的值來刪除它
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo']
>>> marxes.remove('Gummo')
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo']
```

#### 用 `pop()`與位移值來取的一個項目，並刪除他
* 如果呼叫 `pop()`並使用一個位移值，他會回傳該位移值項目，如果未使用引數，他會使用-1
* `pop(0)`會回傳串列的開頭，`pop()`與 `pop(-1)`會回傳結尾
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> marxes.pop()
'Zeppo'
>>> marxes
['Groucho', 'Chico', 'Harpo']
>>> marxes.pop(1)
'Chico'
>>> marxes
['Groucho', 'Harpo']
```

#### 用 `index()` 與值來尋找某個項目的位移值
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> marxes.index('Chico')
1
```

#### 用 `in`來測試值
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> 'Groucho' in marxes
True
>>> 'Bob' in marxes
False
```
```python=
>>> words = ['a', 'deer', 'a', 'female', 'deer']
>>> 'deer' in words
True
```

> 若經常檢查串列中某些值是否存在，且不在乎項目的順序，Python **集合** 更適合用來儲存及查看獨一無二的值

#### 用 `count()` 來算出某個值的出現次數
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> marxes.count('Harpo')
1
>>> marxes.count('Bob')
0

>>> snl_skit = ['cheeseburger', 'cheeseburger', 'cheeseburger']
>>> snl_skit.count('cheeseburger')
3
```

#### 用 `join()`來轉換字串
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> ', '.join(marxes)
'Groucho', 'Chico', 'Harpo'
```

* `join()`是一種字串方法，不是串列方法，不能使用 `marxes.join(, )`
* `join()`的引數是一個字串，或任何可迭代的字串序列，他輸出是一個字串，如果 `join()` 只是一個串列方法，你就不能將它用在其他可迭代物件，ex: tuple 或字串
* `join()` 是 `split()`的相反
```python=
>>> friends = ['Harry', 'Hermione', 'Ron']
>>> separator = ' * '
>>> joined = separator.join(friends)
>>> joined
'Harry * Hermione * Ron'

>>> separated = joined.split(separator)
>>> separated
['Harry', 'Hermione', 'Ron']
>>> separated == friends
True
```

#### 用 `sort()`來排序項目
* `Python` 提供兩種函式：
	* 串列函式 `sort()` 會**就地**排序串列本身
	* 通用函式 `sorted()`會排序串列，之後回傳**複本**
* 如果串列項目是數字，在預設情況下，`Python`會昇冪排列數字，若是字串，`Python`會按照字母順序來排列
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> sorted_marxes = sorted(marxes)
>>> sorted_marxes
['Chico', 'Groucho', 'Harpo']
```

* `sorted_marxes`是一個複本，建立他並不會改變原本的串列
```python=
>>> marxes 
['Groucho', 'Chico', 'Harpo']
```

* 但對 `marxes`串列呼叫串列函式 `sort()`，會改變 `marxes` 
```python=
>>> marxes.sort()
>>> marxes 
['Groucho', 'Chico', 'Harpo']
```
```python=
>>> numbers = [2, 1, 4.0, 3]
>>> numbers.sort()
>>> numbers 
[1, 2, 3, 4.0]
```
```python=
>>> numbers = [2, 1, 4.0, 3]
>>> numbers.sort(reverse=True)
>>> numbers 
[4.0, 3, 2, 1]
```

#### 使用 `len()`來取得長度
```python=
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> len(marxes)
3
```

#### 用 `=`來指派，用`copy()`來複製
```python=
>>> a = [1, 2, 3]
>>> a
[1, 2, 3]
>>> b = a 
>>> b
[1, 2, 3]
>>> a[0] = 'surprise'
>>> a
['surprise', 2, 3]
>>> b
['surprise', 2, 3]
```
```python=
>>> b
['surprise', 2, 3]
>>> b[0] = 'I hate surprises'
>>> b
['I hate surprises', 2, 3]
>>> a 
['I hate surprises', 2, 3]
```

* 可以使用以下方法，將串列的值**複製**到一個獨立、全新的串列：
1. 串列的 `copy()` 函式
2. `list()`轉換函式
3. 串列 `slice[:]`
* 原始串列仍然是a，用串列 `copy()`函式來製作b，用 `list()`轉換函式來製作c，用串列 `slice`來製作d
```python=
>>> a = [1, 2, 3]
>>> b = a.copy() 
>>> c = list(a)
>>> d = a[:]
```
* b, c, d都是a的**複本**：他們都是新的物件，擁有自己的值，且與a所參考的原始串列物件[1, 2, 3]沒有關聯，改變 a**不會**影響b, c, d的複本：
```python=
>>> a[0] = 'integer lists are boring'
>>> a
['integer lists are boring', 2, 3]
>>> b
[1, 2, 3]
>>> c
[1, 2, 3]
>>> d
[1, 2, 3]
```