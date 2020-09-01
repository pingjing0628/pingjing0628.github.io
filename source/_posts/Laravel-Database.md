---
title: Laravel Database
date: 2019-10-27 15:49:59
tags: 
    - Laravel
    - Database
    - Collection
categories: Laravel
---
Laravel 將Model分成三個部分
1. 原生 SQL 查詢
2. 查詢建構器
3. Eloquent ORM

<!--more-->
---

## 原生 SQL 查詢

如果資料庫設定完成，接著用 `DB::connection('資料庫類型')` 連線後，就可以使用 `DB` facade 進行查詢。DB facade 提供每個類型的查詢方法：`select`、`update`、`insert`、`delete`、`statement`。

### 基本使用方法
```php=
// 連線到資料庫
DB::connection('mysql');

//select
$users = DB::select('select * from users where active = ?', [1]);
//insert
DB::insert('insert into users (id, name) values (?, ?)', [1, 'Dayle']);
//update
$affected = DB::update('update users set votes = 100 where name = ?', ['John']);
//delete，後面一樣可以接 where 語句設定條件
$deleted = DB::delete('delete from users where id = ?', [1]);
//一般陳述式
DB::statement('drop table users');

```

1. 這些方法都有兩個可以參數帶入
    * 第一個是資料庫訪查語句(型別是字串)，第二個是訪查參數(型別是數字或字串的陣列)。
    * 訪查語句有幾個「?」，訪查參數就要放幾個參數，而且是依序對應到前面的「?」。
2. select、update、delete 會有一個回傳值
    * select 回傳資料庫內容，以「陣列」的形式存放。
    * update、delete 回傳資料庫受影響的行數。

#### select 查詢出來的結果，都是以陣列方式存放，而裡面每筆資料都是一個 PHP `stdClass` 的物件。
1. 由於取出來的資料是陣列，所以如果從資料庫撈出來的資料想再作處理，就要比照陣列的處理模式，例如 foreach 、 while ...
2. 由於每筆資料都是 `stdClass`，所以可以把存取的欄位當作物件的一個屬性來存取每個欄位的值
```php=
// 連線到資料庫
DB::connection('mysql');

//select
$users = DB::select('select * from users where active = ?', [1]);

foreach ($users as $user) {
    echo $user->name;
}
```

### 監聽查詢事件
Laravel 5.1之後取消了 `getQueryLog()`，取而代之的是`DB::listen()`

1. import use `Illuminate\Support\Facades\Log;`
2. 透過 `Log::info()` 把記錄存在 Log file(路徑：/storage/logs)
```php=
<?php

namespace App\Providers;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * 啟動任何應用程式服務。
     *
     * @return void
     */
    public function boot()
    {
        DB::listen(function ($query) {
            Log::info($query->sql);
            // $query->bindings
            // $query->time
        });
    }
}

```

### Database Transactions
在資料存取資料庫時，最怕出了什麼差錯，導致資料處理不連續，最終導致資料錯亂。這如同銀行轉賬，如果轉出帳戶扣款了，但是轉入帳戶發生意外沒寫入資料庫，那麼這次轉賬就宣告失敗，轉出帳戶的金額要復原。

為了避免 sql 原生語法有 `transaction` 的機制，整個程序中一但有一步資料沒處理好，就整個程序都取消，過程中已經執行過的動作全部復原！

在 Laravel 中更簡便了這個動作，呼叫 `transaction()` 將資料庫查訪動作寫在閉包裡面，一但閉包成功，就完成程序；閉包失敗，就自動還原資料庫。不用再特地手動提交或還原了。

使用方法如下
```php=
DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);

    DB::table('posts')->delete();
});

```

---

## 查詢建構器

查詢建構器最大的好處就是簡化了查訪資料庫的語句，也就是不用再寫一堆煩人 sql 語句。

### 基本使用方法
```php= 
// 連線到資料庫，使用原生 sql 語法才需要
DB::connection('mysql');

//取得資料
//原生 sql 語法
$users = DB::select('select * from users');
//使用查詢建構器
$users = DB::table('users')->get();
```
* `get` 方法取得的結果，都是 `Illuminate\Support\Collection` (集合)實例，每個都是 PHP `StdClass` 物件。
* 由於取出來的資料是 `集合`，所以如果資料想再作處理，可以直接在用集合的鏈結語法處理資料，例如 `pluck` `chunk` ...
* 由於每筆資料都是 `stdClass`，所以可以把存取的欄位當作物件的一個屬性來存取每個欄位的值

### 鏈結語法
`$users = DB::table('users')->where('id', '>', 0)->get();`

使用「鏈結語法」的時候就要特別注意，現在是還在「查詢建構器」裡面，還是已經取得結果了，這決定了後面可用的「鏈結語法」為何。

以上面的例子來看到 `where` 語句結束時都還屬於下達搜尋指令，也就是還在 `Builder` 裡面，也就是說我們可以繼續鏈結 `where` 、 `join` 、 `orderBY` ...等方法。
但是一但鏈結了 `get`，他就會變更為集合實例，就只能鏈結集合可以用的方法了。

---

## Eloquent ORM
* Eloquent ORM 提供了簡單、漂亮的語法來和資料庫互動。而實際做法就是每個資料表都對應著一個 `Model` 來和資料表互動。

### 定義模型
* 我們可以透過 `Artisan` 指令快速建立 `Model`
```cmd= 
php artisan make:model Flight
//Flight 為 Model 名稱
```

### 基本表示法

透過 `Artisan` 指令可以生成一個最基本的 `Model` 如下：
```php=
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    //
}

```
從上述程式碼可以得知，新創立的「模型」：

* 會存放在 `App` 資料夾下
* 繼承 `Illuminate\Database\Eloquent\Model`

### 預設條件
1. **資料表名稱**

`Eloquent` 本身會默認先找一個與「模型」名稱一樣，但是是小寫、底線、複數形式的名詞來當作資料表名稱。


| Class Name | Default Table Name |
| -------- | -------- |
| Flight     | flight     |
| User     | user     |
| MyTable     | my_tables     |

當然你也可以直接在「模型」裡用 `$table` 屬性設定資料表名稱
```php=
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    protected $table = 'my_flights';
    // 自定義資料表名稱為 'my_flights'
}

```
2. **主鍵**

`Eloquent` 也會假設每個資料表有一個遞增的整數值主鍵欄位叫做 id。你可以定義一個 `$primaryKey` 屬性來重新定義。

此外，如果你的主鍵非遞增方式，請務必把 `public $incrementing` 屬性設定為 `false`。如果非整數值，則請把 `protected $keyType` 屬性設定為 `string`。

3. **時間戳記**

`Eloquent` 預設你的資料表會有 `created_at` 和 `updated_at` 來記錄資料寫入和更新資料表的時間，如果不需要這些設定，可以把 `public $timestamps` 屬性設定為 `false`。

也可以透過 `protected $dataFormat` 來變更日期儲存格式，以及輸出成陣列或 JSON 時的格式。

除此之外，你也可以自定義 `CREATED_AT` 和 `UPDATED_AT` 常數來決定資料表中的欄位名稱。
```php=
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    /**
     * 模型的日期欄位儲存格式。
     *
     * @var string
     */
    protected $dateFormat = 'U';
    
    //自定義 資料寫入和更新資料表 的欄位名稱
    const CREATED_AT = 'creation_date';
    const UPDATED_AT = 'last_update';
}
```

4. **資料庫連線**

基本上資料庫連線會先依照 `config\database.php` 的設定，但你也可以透過 `$connection` 屬性為 `Eloquent` 指定不同連線。


---

## 集合

`Eloquent` 回傳的負數資料結果，都會是 `Illuminate\Database\Eloquent\Collection` 的實例，這跟「查詢建構器」的方法 `get()` 一樣。

### 什麼是「集合」`collection` ?
集合物件繼承 Laravel collections，所以它自然也繼承了幾十種用於與底層 `Eloquent` 模型陣列的優雅方法。

集合相比陣列強大許多，除了提供 `map`、`sort` 或 `reduce` 等各種遍歷集合的方法外，最大的好處就是可以透過「鏈結語法」的直觀操作。

ForExample：
假設 users 表中有一個欄位 `is_online` 記錄了目前使用者在線上，我們可以透過 `reject()`、`map()` 的方式找出目前在線上的使用者姓名。
```php=
$name = App\Flight::all()
        ->reject(function ($user) {
                return $user->is_online === 0;
            })
        ->map( function ($user) {
                return $user->name;
            });
```