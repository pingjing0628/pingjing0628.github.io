---
title: 2. Laravel blade
date: 2019-10-27 11:14:14
tags: 
- Laravel
- view
categories: Laravel
---
## Laravel blade

<!--more-->
***

* When you see route to trace controller and then when you see `return view('home');`
* means you have to find `home.blade.php`
* `@extends('layouts.app')`
表示此blade的基礎view
* `@section（'content'）... @endsection`
這兩個代碼之間的程式，會被放到基礎view的`@yield('content')`這個區塊輸出
* 刪除homeController 中的 建構子即可取消登入
```php=
public function __construct()
{
    $this->middleware('auth');
}
```
* 接下來引入model，於Controller的index更改
```php=
public function index()
{
    return view('home')->withArticles(\App\Article::all());
}
```
* `\App\Article::all()` 是以**絕對命名空間的方式** 對Article 調用。
* `withArticles` 是我定義的方法，Laravel View 採用`__call` 来 handle 對未定義 function 的調用，给view注入 $articles 的變數，這行code等於 `->with('articles', \App\Article::all())`
* `->withFooBar(100)` 等於 `->with('foo_bar', 100)`，即駝峰變數會被轉換為蛇形變數。 
* 修改view文件
```html=
@extends('layouts.app')

@section('content')
    <div id="title" style="text-align: center;">
        <h1>Learn Laravel 5</h1>
        <div style="padding: 5px; font-size: 16px;">Learn Laravel 5</div>
    </div>
    <hr>
    <div id="content">
        <ul>
            @foreach ($articles as $article)
            <li style="margin: 50px 0;">
                <div class="title">
                    <a href="{{ url('article/'.$article->id) }}">
                        <h4>{{ $article->title }}</h4>
                    </a>
                </div>
                <div class="body">
                    <p>{{ $article->body }}</p>
                </div>
            </li>
            @endforeach
        </ul>
    </div>
@endsection
```

* 調整view，以增加頂端菜單欄
```html=
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>Learn Laravel 5</title>

    <link href="//cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <script src="//cdn.bootcss.com/jquery/2.2.4/jquery.min.js"></script>
    <script src="//cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
</head>

    <div id="title" style="text-align: center;">
        <h1>Learn Laravel 5</h1>
        <div style="padding: 5px; font-size: 16px;">Learn Laravel 5</div>
    </div>
    <hr>
    <div id="content">
        <ul>
            @foreach ($articles as $article)
            <li style="margin: 50px 0;">
                <div class="title">
                    <a href="{{ url('article/'.$article->id) }}">
                        <h4>{{ $article->title }}</h4>
                    </a>
                </div>
                <div class="body">
                    <p>{{ $article->body }}</p>
                </div>
            </li>
            @endforeach
        </ul>
    </div>

</body>
</html>
```
* view 變成一個獨立的view，不再有基底view，并將 jQuery 和 BootStrap 替換為CDN，更快更稳定了。
* 同理也更改 `app.blade.php`
* 删除 `<script src=""></script>`和
* 替换 `<link href="" rel="stylesheet">`為
```html=
<link href="//cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
<script src="//cdn.bootcss.com/jquery/2.2.4/jquery.min.js"></script>
<script src="//cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
```
