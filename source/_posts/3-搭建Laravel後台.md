---
title: 3. 搭建Laravel後台
date: 2019-10-27 15:46:51
tags: Laravel
categories: Laravel
---
## 搭建Laravel後台

<!--more-->
***

1. 使用Artisan 去搭建後台
`php artisan make:controller Admin/HomeController`
此時 laravel會幫我們自己建一個admin資料夾在controller裡面

2. 增加路由於web.php
```php=
Route::group(['middleware' => 'auth', 'namespace' => 'Admin', 'prefix' => 'admin'], function() {
    Route::get('/', 'HomeController@index');
});
```
於是在網址後方加上`/admin`即可

3. 新建index首頁，在admin/Homecontroller加上
```php=
public function index()
{
    return view('admin/home');
}
```
4. create 後台首頁view
* create admin folder in resource/views
* create home.blade.php
```html=
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-10 col-md-offset-1">
            <div class="panel panel-default">
                <div class="panel-heading">Learn Laravel 5 後台</div>

                <div class="panel-body">

                    <a href="{{ url('admin/article') }}" class="btn btn-lg btn-success col-xs-12">管理文章</a>

                </div>
            </div>
        </div>
    </div>
</div>
@endsection
```

5. 修改 Auth 系統登入成功之後的跳轉路徑
`Controllers/Auth/LoginController.php`
```php=
protected $redirectTo = '/admin';
```

## 開始建立 Article 管理功能
1. add route
```php=
Route::group(['middleware' => 'auth', 'namespace' => 'Admin', 'prefix' => 'admin'], function() {
    Route::get('/', 'HomeController@index');
    Route::get('article', 'ArticleController@index');
});
```
2. create controller
`php artisan make:controller Admin/ArticleController
`
3. 新增index方法
```php=
public function index()
{
    return view('admin/article/index')->withArticles(Article::all());
}
```
4. 新增view
* 在resources/views/admin增加article folder
* create a index.blade.php in article folder
* 然後貼上以下
```html=
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-10 col-md-offset-1">
            <div class="panel panel-default">
                <div class="panel-heading">文章管理</div>
                <div class="panel-body">
                    @if (count($errors) > 0)
                        <div class="alert alert-danger">
                            {!! implode('<br>', $errors->all()) !!}
                        </div>
                    @endif

                    <a href="{{ url('admin/article/create') }}" class="btn btn-lg btn-primary">新增</a>

                    @foreach ($articles as $article)
                        <hr>
                        <div class="article">
                            <h4>{{ $article->title }}</h4>
                            <div class="content">
                                <p>
                                    {{ $article->body }}
                                </p>
                            </div>
                        </div>
                        <a href="{{ url('admin/article/'.$article->id.'/edit') }}" class="btn btn-success">编辑</a>
                        <form action="{{ url('admin/article/'.$article->id) }}" method="POST" style="display: inline;">
                            {{ method_field('DELETE') }}
                            {{ csrf_field() }}
                            <button type="submit" class="btn btn-danger">删除</button>
                        </form>
                    @endforeach

                </div>
            </div>
        </div>
    </div>
</div>
@endsection
```

5. 需要增加調用
`use App\Article;`