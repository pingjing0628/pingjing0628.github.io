---
title: PHP vs GoLang
date: 2019-12-09 23:10:51
tags: 
  - GoLang
  - PHP
  - Defer
categories: 
  - GoLang
  - PHP
---

## PHP vs GoLang

<!--more-->
---

### 定義變數－Variables

**PHP**
```php=
$a = "foo";
$b = "bar";
```

**GoLang**
區分為「新定義」、「預先定義」、「自動新定義」、「覆蓋」。
```go=
// 新定義：定義新的 a 變數為字串型別，且值是「foo」
var a string = "foo"

// 預先定義：先定義一個新的 b 變數為字串型別但不賦予值
var b string

// 自動新定義：讓 Golang 依照值的內容自己定義新變數的資料型態
c := "bar"

// 覆蓋：先前已定義過 a ，可以直接覆蓋其值
a = "fooooooo"  
```

### 輸出－Echo
在 PHP 中常用到 `echo` 來顯示文字。
**PHP**
```php=
echo "Foo"; // 輸出：Foo

$A = "Bar"
echo $A; // 輸出：Bar

$B = "Hello"
echo $B . ", world!"; // 輸出：Hello, world!

$C = [1, 2, 3];
echo var_dump($C); // 輸出：array(3) {[0]=>int(1) [1]=>int(2) [2]=>int(3)}  
```

**GoLang**
在 Golang 中需要 `fmt` 套件。
```go=
fmt.Println("Foo") // 輸出：Foo

A := "Bar"  
fmt.Println(A) // 輸出：Bar

B := "Hello"  
fmt.Printf("%s, world!", B) // 輸出：Hello, world!

C := []int{1, 2, 3}  
fmt.Println(C) // 輸出：[1 2 3]  

```

### 函式－Function
相差較少
**PHP**
```php=
function test() {  
    return "Hello, world!";
}

echo test(); // 輸出：Hello, world!  
```

**GoLang**
Golang 須在函式後面宣告會回傳的資料型別。

```go=
func test() string {  
    return "Hello, world!"
}

fmt.Println(test()) // 輸出：Hello, world! 
```

### 多值回傳－Multiple Value

**PHP**
在 PHP 中，回傳多個資料需使用陣列，將資料放入陣列裡。
```php=
function test() {  
    return [
        'username' => 'yu0628', 
        'time'     => 123456
    ];
}
$data = test();

echo $data['username'], $data['time']; // 輸出：yu0628 123456  
```

**GoLang**
在 Golang 中不必用到一個陣列，函式可以一次回傳多個值：
```go=
func test() (string, int) {  
    return "yu0628", 123456
}
username, time := test()

fmt.Println(username, time) // 輸出：yu0628 123456  

```

### 匿名函式－Anonymous Function

**PHP**
```php=
$a = function() {
    echo "Hello, world!";
};

$a(); // 輸出：Hello, world!
```

**GoLang**
```go=
a := func() {  
    fmt.Println("Hello, world!")
}

a() // 輸出：Hello, world!  
```

### 多資料儲存型態－Stores

**PHP**
PHP 儲存皆用陣列即可。
```php=
$array  = [1, 2, 3, 4, 5];
$array2 = ['username' => 'yu0628', 
           'password' => '2019 Dec'];
```

**GoLang**
Golang 中有這些型態：`array`, `slice`, `map`, `interface`
Golang 是個**強型別語言**，意思是陣列中只能有一種型態，means當決定此陣列是擺放字串資料時，就只能放字串。沒有數值、沒有布林值。

#### 1. 陣列－Array

>一個存放固定長度的陣列。

Golang 定義陣列時，須給一個長度及其內容存放的資料型態，陣列內容不一定要填滿其長度，但陣列內容不能超過當初定義的長度。

**PHP**
```php=
$a = ["foo", "bar"];

echo $a[0]; // 輸出：foo  
```

**GoLang**
```go=
var a [2]string

a[0] = "foo"  
a[1] = "bar"

fmt.Println(a[0]) // 輸出：foo 
```

#### 2. 切片－Slice

>可供「裁切」而且供自由擴展的陣列。

相較於陣列的好處：「不用定義其最大長度，而且你可以直接賦予值」。

**PHP**
```php=
$a = ["foo", "bar"];

echo $a[0]; // 輸出：foo  

```

**GoLang**
```go=
a := []string{"foo", "bar"}

fmt.Println(a[0]) // 輸出：foo  
```

像是 PHP 中的 `array_slice()`，但 Golang 直接讓 `Slice`「內建」了，其用法是：`slice[開始:結束]`。

**GoLang**
```go=
p := []int{1, 2, 3, 4, 5, 6}

fmt.Println(p[0:1]) // 輸出：[1]  
fmt.Println(p[1:1]) // 輸出：[]  （！注意跟 PHP 不一樣！）  
fmt.Println(p[1:])  // 輸出：[2, 3, 4, 5, 6]  
fmt.Println(p[:1])  // 輸出：[1]  
```

**PHP**
```php=
$p = [1, 2, 3, 4, 5, 6];

echo array_slice($p, 0, 1); // 輸出：[1]  
echo array_slice($p, 1, 1); // 輸出：[2]  
echo array_slice($p, 1);    // 輸出：[2, 3, 4, 5, 6]  
echo array_slice($p, 0, 1); // 輸出：[1]  
```

#### 3. 映照－Map

>有鍵名和鍵值的陣列。

「需要事先定義其鍵名、鍵值的資料型態」，仍限制無法在映照中存放多種不同型態的資料。

**PHP**
```php=
$data["username"] = "yu0628";
$data["password"] = "2019 Dec";

echo $data["username"]; // yu0628
```


**GoLang**
在 Golang 需要先用 `make()` 宣告 `map`。

```go=
data := make(map[string]string)

data["username"] = "yu0628"  
data["password"] = "2019 Dec"

fmt.Println(data["username"]) // yu0628  
```

#### 4. 接口－Interface

>一個可存放多種資料型態的陣列。

**PHP**
```php=
$mixedData  = ["foobar", 123456];
$mixedData2 = ['username' => 'yu0628', 
               'time'     => 123456];
```

**GoLang**
Golang 的 `interface{}` 可以接受任何內容，可以存放任何型態的資料
```go=
mixedData := []interface{}{"foobar", 123456}

mixedData2 := make(map[string]interface{})  
mixedData2["username"] = "yu0628"  
mixedData2["time"]     = 123456  
```

### 不定值－Mixed Type
有個不定值的變數，在 PHP 可以直接將一個變數定義成字串、數值、空值。

**PHP**
```php=
$mixed = 123;
echo $mixed; // 輸出：123

$mixed = 'Moon, Dalan!';
echo $mixed; // 輸出：Moon, Dalan!

$mixed = ['A', 'B', 'C'];
echo $mixed; // 輸出：['A', 'B', 'C']  
```

**GoLang**
在 Golang 中給予變數一個指定的資料型別
```go=
var mixed interface{}

mixed = 123  
fmt.Println(mixed) // 輸出：123

mixed = "Moon, Dalan!"  
fmt.Println(mixed) // 輸出：Moon, Dalan!

mixed = []string{"A", "B", "C"}  
fmt.Println(mixed) // 輸出：["A", "B", "C"]  

```

### 逆向處理－Defer
當程式中不需要繼續使用到某個資源或是發生錯誤時，會將其關閉或是拋棄來節省資源開銷，如 PHP 裡的讀取檔案：
**PHP**
```php=
$handle = fopen('example.txt', 'r');

if($errorA)  
    errorHandlerA();

if($errorB)  
    errorHandlerB();

fclose($handle); // 關閉檔案  
```

**GoLang**
在 Golang 中，使用 `defer` 來在函式結束的時候自動執行某些程式(其執行方向為反向)。就不需要在函式最後面結束最前面的資源。
`defer` 可被稱為「推遲執行」，實際上就是在函式結束後會「反序」執行的東西
例如按照了這樣的順序定義 defer： A->B->C->D，那麼執行的順序其實會是 D->C->B->A，這用在程式結束時還蠻有用的
```go=
handle := file.Open("example.txt")  
defer file.Close() // 關閉檔案但「推遲執行」，所有程式結束後才會執行這裡

if errorA {  
    errorHandlerA()
}
if errorB {  
    errorHandlerB()
}

```
### 迴圈－Loops
Golang 中僅有 `for` 一種迴圈但卻能夠達成 `foreach`、`while`、`for` 多種用法。

**PHP**
```php=
for($i = 0; $i < 3; $i++)  
    echo $i; // 輸出：012

$j = 0;
for($j; $j < 5; $j++)  
    echo $j; // 輸出：01234
```

**GoLang**
在 Golang 請記得：若 `i` 先前並不存在，就需要定義它，所以下面這個範例會 `i := 0`。

```go=
for i := 0; i < 3; i++ {  
    fmt.Println(i) // 輸出 012
}

j := 0  
for ; j < 5 ; j++ {  
    fmt.Println(j) // 輸出：01234
}

```

### 每個－Foreach

**PHP**
```php=
$data = ['a', 'b', 'c'];

foreach($data as $index => $value)  
    echo $index . $value . '|' ; // 輸出：0a|1b|2c|

foreach($data as $index => $value)  
    echo $index . '|' ; // 輸出：0|1|2|

foreach($data as $value)  
    echo $value . '|' ; // 輸出：a|b|c|
```
  
**GoLang**
```go=
data := []string{"a", "b", "c"}

for index, value := range data {  
    fmt.Printf("%d%s|", index, value)  // 輸出：0a|1b|2c|
}

for index := range data {  
    fmt.Printf("%d|", index)  // 輸出：0|1|2|
}

for _, value := range data {  
    fmt.Printf("%s|", value)  // 輸出：a|b|c|
}

```

### 重複－While
`while(條件)` 迴圈在 PHP 裡面可以不斷地執行區塊中的程式，直到 條件 為 `false` 為止。

**PHP**
```php=
$i = 0;

while( $i < 3 ) {  
    $i++;
    echo $i; // 輸出：123
}

while(true)  
    echo "WOW" // 輸出：WOWWOWWOW...
```

**GoLang**
在 Golang 裡也有相同的做法，但仍是透過 `for` 迴圈，此 `for` 迴圈並沒有任何的分號（;），而且一個沒有條件的 `for` 迴圈會一直被執行。
```go=
i := 0

for i < 3 {  
    i++
    fmt.Println(i) // 輸出：123
}

for {  
    fmt.Println("WOW") // 輸出：WOWWOWWOWWOWWOW...
}

```

### 做 .. 重複－Do While

**PHP**
```php=
$i = 0;

do {  
    $i++;
    echo $i; // 輸出：123
} while($i < 3);
```

**GoLang**
在 Golang 中則沒有相關函式，但可以透過一個無止盡的 `for` 迴圈加上條件式使其結束迴圈。
```go=
i := 0

for {  
    i++
    fmt.Println(i) // 輸出：123

    // 注意這個條件式和 PHP 有所不同
    if i > 2 {
        break
    }
}

```

### 日期－Date

**PHP**
```php=
echo date("Y-m-d H:i:s"); // 輸出：2016-07-13 12:59:59  
```

**GoLang**
Golang 並不是以 `Y-m-d` 此格式做為定義，而是 `1、2、3`，這令人需要去翻閱文件，才能夠知道 1 的定義是代表什麼。
```go=
fmt.Println(time.Now().Format("2006-2-1 03:04:00"))          // 輸出：2016-07-13 12:59:59  
fmt.Println(time.Now().Format("Mon, Jan 2, 2006 at 3:04pm")) // 輸出： Mon, Jul 13, 2016 at 12:59pm  
```

### 切割字串－Split

**PHP**
```php=
$data  = 'a, b, c, d';
$array = explode(', ', $data);
```

**GoLang**
```go=
data  := "a, b, c, d"  
array := strings.Split(data, ", ")  
```
記得引用 `strings` 套件。


### 關聯陣列－Associative Array

**PHP**
```php=
$data = ['username' => 'yu0628',
         'password' => '2019 Dec'];

echo $data["username"]; // 輸出：yu0628  
```

**GoLang**
```go=
data := map[string]string{  
           "username": "yu0628", 
           "password": "2019 Dec"}

fmt.Println(data["username"]) // 輸出：yu0628 
```

### 是否存在－Isset
**PHP**
```php=
// 如果 $data['username'] 存在
if(isset($data['username'])) {  
    $username = $data['username'];
}
```

**GoLang**
在 Golang 裡面很簡單的能夠這樣辦到（僅適用於 `map`）。
```go=
username, exists := data["username"]

if !exists {  
    fmt.Printf("你要找的資料不存在。")
}
```

### 指針－Pointer
指針（參照）是一個像是「變數別名」的方法，此方法讓人不用整天覆蓋舊的變數
假設 A = 1; B = A; 此時 B 會複製一份 A 且兩者不相干，倘若希望修改 B 的時候實際上也會修改到 A 的值，就會需要指針。

指針比起複製一個變數，會建立一個指向到某個變數的記憶體位置，這就是為什麼改變指針，實際上是在改變某個變數。

**PHP**
```php=
function zero(&$number) { // & 即是指針  
    $number = 0;
}

$A = 5;
zero($A);

echo $A; // 輸出：0 
```

**GoLang**
在 Golang 需要用上 `*` 還有 `&` 符號。
```go=
func zero(number *int) {  
    number = 0
}

func main() {  
    A := 5;
    zero(&A)

    fmt.Printf("%d", A) // 輸出：0
}

```

### 錯誤處理－Error Exception
有時回傳的陣列裡可能有資料還有錯誤代號，且會用條件式判斷錯誤代號是否非空值。

**PHP**
```php=
function foo($number) {  
    if($number !== 1)
        return ['number' => -1, 
                'error'  => '$number is not 1'];

    return ['number' => $number, 
            'error'  => null];
}

$bar = foo(0);

if($bar['error'])  
    echo $bar['number'], $bar['error']; // 輸出：-1
                                        //      $number is not 1
```

**GoLang**
在 Golang 中函式可以一次回傳多個值。不需要真的回傳一個陣列，不過要注意的是將會回傳一個屬於 `error` 資料型態的錯誤，所以需引用 `errors` 套件幫助。
Golang 沒有 try .. catch，因為 **Golang 推薦**，應在每一次執行可能會發生錯誤的程式時就處理錯誤，而非後來用 try 到處包覆程式。
```go=
import "errors"

func foo(number int) (int, error) {  
    if number != 1 {
        return -1, errors.New("$number is not 1")
    }
    return number, nil
}

if bar, err := foo(0); err != nil {  
    fmt.Println(bar, err) // 輸出：-1
                          //      $number is not 1
}

```
`if` 條件式裡宣告變數會只能在 `if` 內部使用這個變數，而不會污染到全域範圍。


### 拋出和捕捉異常－Try & Catch
**PHP**
```php=
function foo($number) {  
    if($number < 10)
        throw new Exception('$number is less than 10');
    else if($number > 10)
        throw new Exception('$number is greater than 10');
}

try {  
    foo(9);
} catch(Exception $e) {
    echo $e->getMessage(); // 輸出：$number is less than 10
}

try {  
    foo(11);
} catch(Exception $e) {
    echo $e->getMessage(); // 輸出：$number is greater than 10
}
```

**GoLang**
可以使用 Golang 中另類處理錯誤的方式（可以的話盡量避免使用這種方式）：`panic(), recover(), defer`。
可把 `panic()` 當作是 `throw`（丟出錯誤），一但你執行了 `panic()` 程式就會宣告而終，程式結束的時候會呼叫 `defer`，所以接下來要在 `defer` 停止 `panic()`。接著要在 `defer` 內使用 `recover()` 讓程式不再繼續進行結束動作，這就像是捕捉異常。
`recover()` 可以看作 `catch`（捕捉），在 `defer` 裡面用 `recover()` 解決 `panic()`，如此程式就會回歸正常而不會被結束。

```go=
// 建立一個模仿 try&catch 的函式供稍後使用
func try(fn func(), handler func(interface{})) {  
    // 這不會馬上被執行，但當 panic 被執行就會結束程式，結束程式就必定會呼叫 defer
    defer func() { 
        // 透過 recover 來從 panic 狀態中恢復，並呼叫捕捉函式
        if err := recover(); err != nil {
            handler(err)
        }
    }()
    // 執行可能帶有 panic 的程式
    fn()
}

func foo(number int) {  
    if number < 10 {
        panic("number is less than 10")
    }
    if number > 10 {
        panic("number is greater than 10")
    }
}

func main() {  
    try(func() {
        foo(9)
    }, func(e interface{}) {
        fmt.Println(e) // 輸出：number is less than 10
    })

    try(func() {
        foo(11)
    }, func(e interface{}) {
        fmt.Println(e) // 輸出：number is greater than 10
    })
}
```

### 套件／匯入／匯出－Package / Import / Export
**PHP**
```php=
// a.php
<?php  
    $foo = "bar";
?>
```
```php=
// index.php
<?php  
    include "a.php";

    echo $foo; // 輸出：bar
?>
```

**GoLang**
Golang 怎麼透過「套件」解決這個問題
```go=
// a.go
package main

var foo string = "bar" 
```
```go=
// main.go
package main

import "fmt"

func main() {  
    fmt.Println(foo) // 輸出：bar
}
```

`main.go` 中除了引用 `fmt` 套件（為了要輸出結果用的套件）之外完全沒有引用到 `a.go`。
**因為兩者都是屬於 `main` 套件，因此共享同一個區域**。

#### 1. 套件－Package
套件是每一個 `.go` 檔案都必須聲明在 Golang 原始碼中最開端的東西:
`package main`

意味著目前的檔案是屬於 `main` 套件，那麼要如何讓同個套件之間的函式溝通呢？

**PHP**
```php=
// a.php
<?php  
    function foo() {
        // ...
    }
?>
```
```php=
// index.php
<?php  
    include "a.php";

    foo();
?>
```

**GoLang**
```go=
// a.go
package main

func foo() {  
    // ...
}
```
```go=
// main.go
package main

func main() {  
    foo()
}
```

#### 2. 匯入－Import
在 Golang 中沒有引用單獨檔案的方式，必須匯入一整個套件，且記住：「一旦匯入，就一定要使用它」。
```go=
package main

import (  
    "fmt"                           // 引用底層套件
    "time"                          // 這也是底層套件
    "github.com/yamiodymel/teameow" // 來自 Github 的 "teameow" 套件
)

func main() {  
    // 下面使用剛匯入的套件
    fmt.XXX()
    time.XXX()
    teameow.XXX()
}

```
假如不希望使用匯入的套件，只是為了要觸發那個套件的 `main()` 函式而引用，那麼可在前面加上一個底線（`_`）。
```go=
import (  
    _ "fmt"
)
```

如果套件出現了名稱衝突，可以在套件來源前面給一個新的名稱。
```go=
import (  
    "github.com/karisu/teameow"
    neko "github.com/yamiodymel/teameow"
)

func main() {  
    teameow.XXX()
    neko.XXX()
}

```

#### 3. 匯出－Export
同個套件內的函式還有共享變數確實可以直接用，但不表示可以給其他套件使用，其方法取決於**函式／變數的「開頭大小寫」**。
**Golang 依照一個函式／變數的開頭大小寫決定這個東西是否可供「匯出」**。
```go=
// a.go
package hello

// 注意：這裡的 Foo 的開頭字母是大寫！
var Foo string = "bar"

// 注意：這個 World 函式的開頭字母是大寫！
func World() {  
    // ...
}

```
```go=
// b.go
package test

import (  
    "hello"
    "fmt"
)

func main() {  
    fmt.Println(hello.Foo) // 輸出：bar

    hello.World()
}

```
用在區別函式時格外有用，小寫開頭的任何事物都是不供匯出的，反之，大寫開頭的任何事物都是用來匯出供其他套件使用的。

### 類別－Class
Golang 中沒有類別，但有所謂的「建構體（Struct）」和「接口（Interface）」

**PHP**
```php=
class Foobar {  
    public $a = "hello, world";

    public function test() {
        echo $this->a;
    }
}

$b = new Foobar();
$b->test(); // 輸出：hello, world!
```

**GoLang**
首先知道在 Golang 中「類別」的成員還有方法都是在「類別」外面所定義的，與 PHP 在類別內定義的方式不同
```go=
// 先定義一個 Foobar 建構體，有個 a 的字串成員
type Foobar struct {  
    a string
}

// 定義一個屬於 Foobar 的 test 方法
func (f *Foobar) test () {  
    // 接收來自 Foobar 的 a（等同於 PHP 的 `$this->a`）
    fmt.Println(f.a)
}

b := &Foobar{a: "hello, world!"}  
b.test() // 輸出：hello, world!  
```

#### 1. 建構子－Constructor
在 PHP 中，當有一個類別被 `new` 的時候會自動執行該類別內的建構子（`__construct()`），通常會用這個來初始化一些類別內部的值。

**PHP**
```php=
class Test{  
    public $a;

    function __construct() {
        $this->a = "foobar";
    }

    function show() {
        echo $this->a;
    }
}

$b = new Test();
$b->show(); // 輸出：foobar
```

**GoLang**
但在 Golang 裡因為沒有類別，也就沒有建構子，不巧的是建構體本身也不帶有建構子的特性，此時只能自己在外部建立一個建構用函式。
```go=
type Test struct {  
    a string
}

func (t *Test) show() {  
    fmt.Println(t.a)
}

// 用來建構 Test 的假建構子
func newTest() (test *Test) {  
    test = &Test{a: "foobar"}

    // 這裡會回傳一個型態是 *Test 建構體的 test 變數
    return
}

b := newTest()  
b.show() // 輸出：foobar  

```

#### 2. 嵌入－Embed
假設有兩個類別，會把其中一個類別傳入到另一個類別裡面使用
**PHP**
```php=
class Foo {  
    public $msg = "Hello, world!";
}

class Bar {  
    public $foo;

    function __construct($foo){ 
    	$this->foo = $foo;    
    }
    
    function show() {
     echo $this->foo->msg; 
 	}
}

$a = new Foo();
$b = new Bar($a);
$b->show(); // 輸出：Hello, world!
```

**GoLang**
在 Golang 中也有相同的用法，但請記得：「**任何東西都是在「類別」外完成建構的**」。
```go=
type Foo struct {  
    msg string
}

type Bar struct {  
    *Foo
}

func (b *Bar) show() {  
    // Foo 中的 msg 會直接暴露在 Bar 底下
    // 所以可以直接使用 b.msg
    fmt.Println(b.msg)
}

a := &Foo{msg: "Hello, world!"}  
b := &Bar{a}  
b.show() // 輸出 Hello, world!  

```

#### 3. 遮蔽－Shadowing
在 PHP 中沒有相關的範例
Golang 在進行 `Foo` 嵌入 `Bar` 時，會自動將 `Foo` 的成員暴露在 `Bar` 底下，那麼假設「雙方之間有相同的成員名稱」呢？
此時被嵌入的成員就會被「遮蔽」

```go=
type Foo struct {  
    msg string
}

type Bar struct {  
    *Foo
    msg string // 遮蔽了 Foo 的 msg
}

a := &Foo{msg: "Hello, world!"}  
b := &Bar{Foo: a, msg: "Moon, Dalan!"}

fmt.Println(b.msg)     // 輸出：Moon, Dalan!  
fmt.Println(b.Foo.msg) // 輸出：Hello, world! 
```

#### 4. 多形－Polymorphism
雖然都是呼叫同一個函式，但這個函式可以針對不同的資料來源做出不同的舉動，這就是多形。
也能夠把這看作是：「訊息的意義由接收者定義，而不是傳送者」。

目前 PHP 中沒有真正的「多形」，不過仍可以做出同樣的東西。
**PHP**
```php=
class Foo
{ 
	public $msg = "hello";  
}  

class Bar
{
	public $msg = "world!"; 
}

class Handler 
{  
    public function process($class) 
    {
        switch(get_class($class)) {
            // 依照不同的資料類型做出不同的舉動
            case 'Foo':
                echo '處理 Foo | ' . $class->msg . ', world!';
                break;

            case 'Bar':
                echo '處理 Bar | ' . 'hello, ' . $class->msg;
                break;
        }
    }
}

$foo = new Foo();
$bar = new Bar();
$handler = new Handler();

// 雖然都是同個函式，但是可以處理不同資料
$handler->process($foo); // 輸出：處理 Foo | hello, world!
$handler->process($bar); // 輸出：處理 Bar | hello, world!
```

**GoLang**
在 Golang 中有 interface 可以幫忙完成這個工作。
```go=
type Foo struct {  
    msg string
}

type Bar struct {  
    msg string
}

// 透過 Handler 實作 process
type Handler interface {  
    process()
}

// 處理 Foo 資料的 process
func (f Foo) process() {  
    fmt.Printf("處理 Foo | %s, world!", f.msg)
}

// 處理 Bar 資料的 process
func (b Bar) process() {  
    fmt.Printf("處理 Bar | hello, %s", b.msg)
}

foo := Foo{msg: "hello"}  
bar := Bar{msg: "world!"}

// 雖然都是同個函式，但是可以處理不同資料
Handler.process(foo) // 輸出：處理 Foo | hello, world!  
Handler.process(bar) // 輸出：處理 Bar | hello, world! 
```