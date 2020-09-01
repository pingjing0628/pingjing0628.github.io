---
title: 1. Laravel Practice
date: 2019-10-27 00:08:28
tags: 
- Laravel
- Database
categories: Laravel
---

# Installation

<!--more-->
***

## Install with docker 
(using richarvey now , should create by myself)
1. 透過 `composer global require laravel/installer ` 去安裝
2. 會面臨到 `bash:laravel command not found`的問題
3. 透過 `export PATH="~/.composer/vendor/bin:$PATH"` 去解決
4. 接著， `laravel new project` (記得要加-f )
5. 記得去改 `vi /etc/nginx/sites-enabled/*`
6. 更改裡面檔案路徑`/project/public`

以上是以 nginx 環境操作

## DB setting
1. via `php artisan make:auth` create 登入畫面
2. via `php artisan migrate` create DB structure
3. 根據 `database/migrations/2014_10_12_000000_create_users_table.php` 去部署 DB structure

## Eloquent
* Eloquent is laravel ORM 
`php artisan make:model Article` Artisan 幫我們生成的Model 文件
* Eloquent讓一個 **Model** 對應一張數據庫表，並且在底層封裝了很多 **function** ，可以讓Model非常方便地調用

* 只需要繼承Eloquent，就可以 use **first() find() where() orderBy()** 等功能
```php=
<?php

class Article extends \Eloquent {

protected $fillable = [];

}
```

#### 基礎使用要點
1. 每一個繼承Eloquent 的類都有兩個'固定用法'
 * `Article::find($number)` 會得到一個帶有DB中取出來值的對象
 * `Article::all()` 會得到一個包含整個DB的對象合集。

2. 所有的中間方法如`where()` `orderBy()` 等都能夠同時支持'靜態' 和'非靜態式' 兩種方式調用
即`Article::where()...`  和 `Article::....->where()`。

3. 所有的'非固定用法' 的調用最後都需要一個操作來'收尾'，本片教程中有兩個'收尾操作'：`->get()` 和`->first()`。

4. 如果不理解為什麼'Article'這個類可以使用`->where()` `->get()`等很多方法的話，需要去讀一下PHP對象繼承的文檔了：對象繼承。


### Model DB manipulation
##### Example
1. Find the article which id = 2 and print it out
```php=
<?php

$article = Article::find(2);
echo $article->title;
```
2. Search and find the title which is "我是標題"，and print id
```php=
<?php

$article = Article::where('title', '我是標題')->first();

echo $article->id;
```
3. Search all article and repeat to print all title
```php=
<?php

$articles = Article::all(); //此處得到的$articles是一個對象集合，可以在後面加上'->toArray()'變成多維數組。

foreach($articles as $article) {

    echo $article->title;
}
```
4. Search all the article which id is between 10~20 and print all the title out
```php=
<?php

$articles = Article::where('id', '>', 10)->where('id', '<', 20)->get();

foreach($articles as $article) {

    echo $article->title;
}
```
5. Search all the article and repeat to print all the title ,按照updated_at 倒序排序
```php=
<?php

$articles = Article::where('id', '>', 10)->where('id', '<', 20)->orderBy('updated_at', 'desc')->get();

foreach($articles as $article) {

    echo $article->title;
}
```

## 使用Migration 和Seeder

### Use artisan 生成 Migration

* `php artisan make:migration create_articles_table`

* A file which name is 2*****_create_articles_table has been create successfully。
We edit its "up" function:
```php=
<?php

public  function  up () 
{ 
    Schema::create('articles' , function ( Blueprint $table )     { 
        $table -> increments( 'id' ); 
        $table -> string( 'title' ); 
        $table -> text( 'body' ) -> nullable(); 
        $table -> integer( 'user_id' ); 
        $table ->timestamps(); 
    });
}
```
* And we make the php into real MySQL database:

`php artisan migrate`

### Use artisan 生成 model and migration

* `php artisan make:model Post -m`

* model本身是個類別，所以採「大駝峰」命名，且名稱最後不加s
* 資料表命名規則為：table名稱小寫，且補上s


### Use artisan 生成 Seeder

* `php artisan make:seeder ArticleSeeder`
* Edit its "run" function
```php=
<?php

public function run()
{
        
	DB::table('articles') -> delete();    

	for($i = 0 ; $i < 10 ; $i ++) { 
	    \App\Article::create(['title'=>'Title'.$i,'body'=>'Body'.$i,'user_id'=>1,]);
	}  
}           
```

* 把ArticleSeeder註冊到系統內。修改DatabaseSeeder.php的 "run" function
```php=
<?php

public  function  run () 
{ 
    $this -> call( ArticleSeeder::class ); 
}    

```
* 執行`php artisan db:seed`
* Refresh articles table，already have 10 rows of fake data