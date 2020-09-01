---
title: 4. Laravel Restful
date: 2019-10-27 15:48:11
tags: 
- Laravel
- CRUD
- Database
categories: Laravel
---
## Restful

<!--more-->
***

1. 進行增刪改查
`Route::resource('photo', 'PhotoController');`
2. 如下

| 動作 | URI| 行為 | 路由名稱 |
| -------- | -------- | -------- | ------ |
| GET     | /photos     | index     | photos.index |
| GET | /photos/create | create | photos.create |
| POST | /photos | store | photos.store |
| GET | /photos/{photo} | show | photos.show |
| GET | /photos/{photo}/edit | edit | photos.edit |
| PUT / PATCH | /photos/{photo} | update | photos.update |
| DELETE | /photos/{photo}| destory | photos.destory|

3. 配置資源路由
* 將`Route::get('article', 'ArticleController@index');` 
* ->改成 `Route::resource('articles', 'ArticleController');`
* article 單數變成複數。

* 修改之前的view
`resources/views/admin/home.blade.php` 中的 ` url('admin/article') ` 
* ->為 ` url('admin/articles') `

* `resources/views/admin/article/index.blade.php` 中的 ` url('admin/article/create') ` 
* ->為 ` url('admin/articles/create') ；`
* 修改 ` url('admin/article/'.$article->id.'/edit') ` 
* ->為 ` url('admin/articles/'.$article->id.'/edit') ；`
* 修改 ` url('admin/article/'.$article->id) ` 
* ->為 ` url('admin/articles/'.$article->id) `

4. 新增article
* 需要兩個動作，1. 新增article的頁面，2. 將資料送到後端
* 新增Article 頁面
    * 使用`/admin/articles/create `建立新增article頁面，瀏覽器使用GET從server獲取，對應`ArticleController`的`create()`方法
    ```php=
    public function create()
    {
        return view('admin/article/create');
    }
    ```
    * 新增view於`resources/views/admin/article/create.blade.php`
```html=
@extends('layouts.app')
@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-10 col-md-offset-1">
            <div class="panel panel-default">
                <div class="panel-heading">新增一篇文章</div>
                <div class="panel-body">

                    @if (count($errors) > 0)
                        <div class="alert alert-danger">
                            <strong>新增失败</strong> 输入不符合要求<br><br>
                            {!! implode('<br>', $errors->all()) !!}
                        </div>
                    @endif

                    <form action="{{ url('admin/articles') }}" method="POST">
                        {!! csrf_field() !!}
                        <input type="text" name="title" class="form-control" required="required" placeholder="请输入标题">
                        <br>
                        <textarea name="body" rows="10" class="form-control" required="required" placeholder="请输入内容"></textarea>
                        <br>
                        <button class="btn btn-lg btn-info">新增文章</button>
                    </form>

                </div>
            </div>
        </div>
    </div>
</div>
@endsection
```

* `view()` 方法是 Laravel 中一个全局的方法，用於調用view文件，接受一個字符串參數，並按照此參數去調取對應的路由。
* `admin/article/create'` 跟 `'admin.article.create'` 是等於的
* 提交資料到後端
    * blade中表單，有csrf_field是laravel內建的應對csrf攻擊的防範措施，任何 POST PUT PATCH 請求都會被檢測是否提交了 CSRF 字段。對應的代碼為 `app/Http/Kernel.php` 裡的 `$middlewareGroups` 屬性裡的 `\App\Http\Middleware\VerifyCsrfToken::class` 值。
    * `{!! csrf_field() !!}` 實際上會生成一个隱藏的 input：`<input type="hidden" name="_token" value="GYZ8OHDAbZICMcEvcTiS82qlZs2XrELklpEl159S">`
    * 如果你的系统有很多的 Ajax，而你又不想降低安全性，這裏的 `csrf_token()` 函數會给你很大的幫助。
    * 提交表單對應的是`store()`方法
    * create store()方法
```php=
    public function store(Request $request) // Laravel 的依賴注入系统會自動初始化我們需要的 Request 類
	{
	    // 數據驗證
	    $this->validate($request, [
	        'title' => 'required|unique:articles|max:255', // 必填、在 articles 表中唯一、最大長度 255
	        'body' => 'required', // 必填
	    ]);

	    // 通過 Article Model 插入一條數據進 articles 表
	    $article = new Article; // 初始化 Article 對象
	    $article->title = $request->get('title'); // 將 POST 提交過了的 title 字段的值賦给 article 的 title 屬性
	    $article->body = $request->get('body'); // 同上
	    $article->user_id = $request->user()->id; // 獲取當前 Auth 系统中註冊的用户，並將其 id 賦给 article 的 user_id 屬性

	    // 將數據保存到數據庫，通過判斷保存结果，控制頁面進行不同跳轉
	    if ($article->save()) {
	        return redirect('admin/articles'); // 保存成功，跳轉到 文章管理頁
	    } else {
	        // 保存失敗，跳回来路頁面，保留用户的輸入，並给出提示
	        return redirect()->back()->withInput()->withErrors('保存失敗！');
	    }
	}
```
5. 編輯Article
* 需要兩個動作： 1. 顯示編輯頁面 2. 將編輯資料送至資料庫更新
* 新增編輯頁面
    * 使用`/admin/article/edit` 建立編輯頁面，瀏覽器使用GET獲取，對應ArticleController 中的`edit()`方法
```php=
public function edit($id ,Request $request)
{
    return view('admin/article/edit')->withArticle(Article::find($id));

}
```
* 新增view於`resources/views/admin/article/edit.blade.php`
```html=
@extends('layouts.app')
@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-10 col-md-offset-1">
            <div class="panel panel-default">
                <div class="panel-heading">編輯 {{$article->title}} 文章</div>
                <div class="panel-body">

                    @if (count($errors) > 0)
                        <div class="alert alert-danger">
                            <strong>修改失敗</strong> 輸入不符合要求<br><br>
                            {!! implode('<br>', $errors->all()) !!}
                        </div>
                    @endif

                    <form action="{{ url('admin/articles/'.$article->id) }}" method="POST">
                        {{ method_field('PATCH') }}
                        {{ csrf_field() }}
                        <input type="text" name="title" class="form-control" required="required" value="{{$article->title}} ">
                        <br>
                        <textarea name="body" rows="10" class="form-control" required="required" >{{$article->body}} </textarea>
                        <br>
                        <button class="btn btn-lg btn-info">修改文章</button>
                    </form>

                </div>
            </div>
        </div>
    </div>
</div>
@endsection
```
* 記得此處post！

* 提交表單到資料庫使用的是`update()`方法

```php= 
public function update($id, Request $request) // Laravel 的依賴注入系统會自動初始化我們需要的 Request 
{
    // 數據驗證
    $this->validate($request, [
        'title' => 'required|unique:articles|max:255', // 必填、在 articles 表中唯一、最大長度 255
        'body' => 'required', // 必填
    ]);
    $article = Article::find($id);
    $article->title = $request->get('title'); // 將 POST 提交過了的 title 字段的值賦给 article 的 title 屬性
    $article->body = $request->get('body'); // 同上
    $article->user_id = $request->user()->id; // 獲取當前 Auth 系统中註冊的用户，並將其 id 賦给 article 的 user_id 屬性
    $article->save();
    // 將數據保存到數據庫，通過判斷保存结果，控制頁面進行不同跳轉
    if ($article->save()) {
        return redirect('admin/articles'); // 保存成功，跳轉到文章管理頁
    } else {
        // 保存失敗，跳回来路頁面，保留用户的輸入，並给出提示
        return redirect()->back()->withInput()->withErrors('保存失敗！');
    }
}
```
6. 刪除Article
* 刪除文章所對應的方法是`destory()`
```php=
public function destroy($id)
{
    Article::find($id)->delete();
    return redirect()->back()->withInput()->withErrors('刪除成功！');
}
```
* 新增view於resources/views/admin/article/delete.blade.php
```html=
<form action="{{ url('admin/articles/'.$article->id) }}" method="POST" style="display: inline;">
    {{ method_field('DELETE') }}
    {{ csrf_field() }}
    <button type="submit" class="btn btn-danger">刪除</button>
</form>
```