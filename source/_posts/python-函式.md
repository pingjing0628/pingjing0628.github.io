---
title: python 函式
date: 2019-11-17 23:42:40
tags:
	- python
	- 函式
categories:
	- Python
---
## Python 函式

<!--more-->
***

### 函式
* 要重複使用程式，第一個步驟是**函式**，他是一種有名稱且獨立的程式片段
* 函式可以取用任何數量與類型的輸入參數，並回傳任何數量與類型的輸出結果
* 可以對函式做兩件事：
1. **定義**它
2. **呼叫**它

* 要定義Python函式，要輸入 `def`，函式名稱、用括號來框住函式的輸入參數，最後加上一個冒號(：)
* 函式名稱的命名規則與變數名稱一樣(他們必須以字母或_開頭，裡面只能使用字母、數字、或_)
```python=
>>> def do_nothing():
        pass
```

* 要呼叫函式，只要輸入他的名稱與括號即可
```python=
>>> do_nothing()
>>> 
```

* 定義並呼叫一個沒有參數，但會印出一個單字的函式
```python=
>>> def make_a_sound():
        print('quack')
>>> make_a_sound()
quack
```

* 定義函式 `echo()`函式
```python=
>>> def echo(anything):
        return anything '' anything

>>> echo('Rumplestiltskin')
'Rumplestiltskin Rumplestiltskin'
```
* 呼叫函式時，傳給他的值稱為 **引數**，若在呼叫時有使用引數，則這些引數的值都會被複製到函式裡面的對應**參數**

```python=
>>> def commentary(color):
        if color == 'red':
            return "It's a tomato."
        elif color == 'green':
            return "It's a green pepper."
        elif color == 'bee purple':
            return "I don't know what it is, but only bees can see it."
        else:
            return "I've never heard of the color" color "."

>>> comment = commentary('blue')
>>> print(comment)
I've never heard of the color blue.
```

* 函式可以取用任何數量、任何類型的輸入引數(包括零)
* 他可以回傳任何數量、任何類型的輸出結果(也包括零)
* 如果函式沒有明確的呼叫 `return`，呼叫方會得到 `None`這個結果
```python=
>>> print(do_nothing())
None
```

#### None 很好用
* `None` 是一個特殊的Python值，他會在沒有東西可說時，保留一個位置
* 他與boolean值 `False`不一樣，雖然將他當成布林值計算時，它會被視為`false`
```python=
>>> thing = None
>>> if thing:
        print("It's something")
    else:
        print("It's nothing")
It's nothing
```

* 要區分 `None` 與 布林值`False`，可以使用Python `is`運算子：
```python=
>>> if thing is None:
        print("It's nothing")
    else:
        print("It's something")
It's nothing
```

* 看起來差異不大，但在Python中非常重要，需要用 `None` 來區分 "遺漏值(missing value)"與 "空值(empty value)"
* 零值的整數或浮點數、空字串('')、串列([])、tuple((,))、字典({})與集合(set())都是`false`，但他們不等於 `None`
```python=
>>> def is_none(thing):
        if thing is None:
            print("It's None")
        elif thing:
            print("It's True")
        else:
            print("It's False")
>>> is_none(None)
It's None
>>> is_none(True)
It's True
>>> is_none(False)
It's False
>>> is_none(0)
It's False
>>> is_none(0.0)
It's False
>>> is_none(())
It's False
>>> is_none([])
It's False
>>> is_none({})
It's False
>>> is_none(set())
It's False
```

#### 位置引數
* 他們的值會被依序複製到對應的參數
* 以下範例會用他的位置引數建立一個字典並將他回傳
```python=
>>> def menu(wine, entree, dessert):
        return {'wine': wine, 'entree': entree, 'dessert': dessert}

>>> menu('chardonnay', 'chicken', 'cake')
{'dessert': 'cake', 'wine': 'chardonnay', 'entree': 'chicken'}
```
* 位置引數的缺點是必須記得每一個位置的意思

#### 關鍵字引數
* 為了避免發生搞不清楚引數的問題，可以用引數的對應參數名稱還指定引數，即使與函式定義中的順序不一樣也不會產生問題
```python=
>>>  menu(wine='bordeaux', entree='beef, dessert='bagel')
{wine='bordeaux', entree='beef, dessert='bagel'}
```
* 可以混合使用位置與關鍵字引數
* 若是在呼叫函式時要同時使用位置與關鍵字引數，位置引數必須放在前面
```python=
>>>  menu('frontenac', entree='fish, dessert='flan')
{wine='frontenac', entree='fish, dessert='flan'}
```

#### 指定預測參數值
* 在呼叫方沒有提供對應的引數時使用
```python=
>>> def menu(wine, entree, dessert='pudding'):
        return ('wine': wine, 'entree': entree, 'dessert': dessert)
```
* 這次在呼叫 `menu()`時不使用 `dessert`引數：
```python=
>>> menu('chardonnay', 'chicken')
{'dessert': 'pudding', 'wine': 'chardonnay', 'entree': 'chicken'}
```
* 若提供引數的話，韓式就會使用它，而不是預設值：
```python=
>>> menu('dunkelfelder', 'duck', 'doughnut')
{'dessert': 'doughnut', 'wine': 'dunkelfelder', 'entree': 'duck'}
``` 

> 預設的引數值會在函式**被定義**的時候計算，而不是在執行的時候
新手常出現的錯誤，就是使用可變的資料類型，例如：串列或字典，來做為預設引數

* 預期 `buggy()`函式在每次執行時，都會使用一個全新的空字串 `result`，並對他加入 `arg`引數，在印出一個單一項目的串列，但這裡有一個bug：這個串列只有在第一次呼叫函式時是空的，第二次呼叫時， `result`仍然存有之前呼叫時留下來的項目：
```python=
>>> def buggy(arg, result=[]):
        result.append(arg)
        print(result)
>>> buggy('a')
['a']
>>> buggy('b') #應該是['b']
['a', 'b']
```
* 如果將它寫成如下，就可以正常運作
```python=
>>> def works(arg):
        result = []
        result.append(arg)
        return result
>>> works('a')
['a']
>>> works('b')
['b']
```
* 以下的修正方式，是傳入其他的東西來說明函式是否第一次被呼叫：
```python=
>>> def nonbuggy(arg, result=None):
    if result is None:
        result = []
    result.append(arg)
    print(result)
>>> nonbuggy('a')
['a']
>>> nonbuggy('b')
['b']
```

#### 用 * 來收集位置引數

* 當在函式的參數使用星號，星號會將可變數量的潛在引數群組化，變成一個參數值的tuple
* `args` 是個參數tuple，他是被傳入 `print_args()`函式的引數所產生的：
```python=
>>> def print_args(*args):
        print('Positional argument tuple:', args)
```
* 當在呼叫時不傳入引數，* args裡面就不會有任何東西
```python=
>>> print_args()
Positional argument tuple:()
```
* 若提供引數，他會被印成 `args tuple`：
```python=
>>> print_args(3, 2, 1, 'wait!', 'uh...')
Positional argument tuple:(3, 2, 1, 'wait!', 'uh...')
```
* 當在編寫 `print()`這類會接收任意數量的引數函式時，若你的函式也需要用到位置引數，就把 `*args`放在最後，抓取剩下的引數：
```python=
>>> def print_more(required1, required2, *args):
    print('Need this one:', required1)
    print('Need this one too:', required2)
    print('All the rest:', args)

>>> print_more('cap', 'gloves', 'scarf', 'monocle', 'mustache wax')
Need this one: cap
Need this one too: gloves
All the rest:('scarf', 'monocle', 'mustache wax')
```
* 使用* 時，並不需要呼叫tuple參數args，這種作法只是python的慣例

#### 用 ** 來收集關鍵字引數

* 使用兩個星號(** )將關鍵字引數群組化，變成一個字典
* 其中引數名稱是鍵，他們的值是對應的字典值
```python=
>>> def print_kwargs(**kwargs):
        print('Keyword arguments:', kwargs)
```
* 呼叫他時，使用一些關鍵引數
```python=
>>> print_kwargs(wine='merlot', entree='mutton', dessert='macaroon')
Keyword arguments: {'dessert': 'macaroon', 'wine': 'merlot', 'entree': 'mutton'}
```
* 在函式內，kwargs是個字典
* 混合使用位置參數與* args & ** kwargs，就必須按照這個順序來排列他們，如同args，其實不需要呼叫關鍵字參數kwargs，這只是一種慣例

#### 函式是第一級公民
* Python的魔咒：**所有東西都是物件**，包括數字、字串、tuple、串列、字典
* 函式也是，可以將他們指派給變數，把他們當引數傳給其他函式，以及在函式中將他們回傳
```python=
>>> def answer():
        print(42)
// 執行時
>>> answer()
42
// 定義另一函式
>>> def run_something(func):
        func()
// 將answer放入
>>> run_something(answer)
42
```
* 傳入的是**answer** 不是**answer()**
* 在Python中，這些括號代表**呼叫這些函式**
* 不使用括號的話，Python會將函式視為其他物件
```python=
>>> type(run_something)
<class 'function'>
```
* 可以將函式當成串列、tuple、集合及字典元素使用，函式是不可變的，所以也可以當成字典鍵使用

#### 內部函式
* 可以在其他函式裡面定義函式
```python=
>>> def outer(a, b):
        def inner(c, d):
            return c + d
        return inner(a, b)

>>> outer(4, 7)
11
```

* 若想要在某個函式裡執行多次複雜的工作，內部函式非常實用，可以避免編寫重複的迴圈或程式碼
```python=
>>> def knights(saying):
        def inner(quote):
            return "We are the knights who says: '%s'" % quote
        return inner(saying)

>>> knights('Ni!')
"We are the knights who says: 'Ni!'"
```

#### Closure
* 內部函式可以扮演closure的角色
* 這是一種由其他韓式動態生成的函式，可以更改或記得函式之外的程式所以建立的變數的值
* 將 `inner()`轉換成一個叫 `inner2()`的closure
```python=
>>> def knights2(saying):
        def inner2():
            return "We are the knights who says: '%s'" % saying
        return inner2
```

* `inner2()`函式知道被傳入的 `saying`得值，並會記得他
* return inner2此行會回傳一個專用的inner2函式複本(而不會呼叫他)
* 這是一個closure：一個被動態建立出來，會記得自己來自哪裡的函式
* 來呼叫
```python=
>>> a = knights2('Duck')
>>> b = knights2('Hasenpfeffer')

// a & b 分別是什麼
>>> type(a)
<class 'function'>
>>> type(b)
<class 'function'>

//它們是函式，也是closure
>>> a
<function knights2.<locals>.inner2 at 0x10193e158>
>>> b
<function knights2.<locals>.inner2 at 0x10193e158>

// 若呼叫他們，他們會記得當自己被建立時所使用的saying
>>> a()
"We are the knights who says: 'Duck'"
>>> b()
"We are the knights who says: 'Hasenpfeffer'"
```
