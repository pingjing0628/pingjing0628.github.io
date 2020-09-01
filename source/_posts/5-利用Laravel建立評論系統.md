---
title: 5.利用Laravel建立評論系統
date: 2019-10-27 15:48:54
tags: 
- Laravel
- Database
categories: Laravel
---

### 基礎規則

* 需要新建一個表專門用來存放每一條評論，每一條評論都屬於某一篇文章。

<!--more-->
***

### 建立 model 和資料表
* 建立名為Comment的model，並順便建立附帶的migration
`php artisan make:model Comment -m`
* 這樣一次性的建立兩個文件
`database/migrations/2017_11_11_151823_create_comments_table.php` 和Comment類
* 填寫`up()`方法，給`comments`表增加
```php=
public function up()
{
    Schema::create('comments', function (Blueprint $table) {
        $table->increments('id');
        $table->string('nickname');
        $table->string('email')->nullable();
        $table->string('website')->nullable();
        $table->text('content')->nullable();
        $table->integer('article_id');
        $table->timestamps();
    });
}
```
* 接著運行
`php artisan migrate`
* 最後資料表已經建立完成

### 建立一對多關係

* 在 Article 模型中增加一對多關係的函數：
```php=
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Article extends Model
{
    public function hasManyComments()
    {
        return $this->hasMany('App\Comment', 'article_id', 'id');
    }
}
```
* 完成！


## 構建前台UI

* 修改前台的視圖文件，想辦法把評論功能加進去。

#### 建立前台的 ArticleController 
`php artisan make:controller ArticleController`
`Route::get('article/{id}', 'ArticleController@show');`
* 此處的 {id} 指任意字符串，此字段為文章 ID，為數字，但是本路由卻會嘗試匹配所有請求，所以當遇到奇怪的路由調用的方法，記得檢查路由順序。路由匹配方式為前置匹配：任何一條路由規則匹配成功，會立刻返回结果，後面的路由便没有響應機會。

* 增加 show 函數
```php=
public function show($id)
{
    return view('article/show')->withArticle(Article::with('hasManyComments')->find($id));
}
```
* 在頂部引入 Model，否則會報錯
```php= 
....
use App\Article;

class ArticleController extends Controller
{
....
}
```

#### 建立前台文章
* 建立`resources/views/article/show.blade.php`
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

    <div id="content" style="padding: 50px;">

        <h4>
            <a href="/"><< 返回首页</a>
        </h4>

        <h1 style="text-align: center; margin-top: 50px;">{{ $article->title }}</h1>
        <hr>
        <div id="date" style="text-align: right;">
            {{ $article->updated_at }}
        </div>
        <div id="content" style="margin: 20px;">
            <p>
                {{ $article->body }}
            </p>
        </div>

        <div id="comments" style="margin-top: 50px;">

            @if (count($errors) > 0)
                <div class="alert alert-danger">
                    <strong>操作失败</strong> 输入不符合要求<br><br>
                    {!! implode('<br>', $errors->all()) !!}
                </div>
            @endif

            <div id="new">
                <form action="{{ url('comment') }}" method="POST">
                    {!! csrf_field() !!}
                    <input type="hidden" name="article_id" value="{{ $article->id }}">
                    <div class="form-group">
                        <label>Nickname</label>
                        <input type="text" name="nickname" class="form-control" style="width: 300px;" required="required">
                    </div>
                    <div class="form-group">
                        <label>Email address</label>
                        <input type="email" name="email" class="form-control" style="width: 300px;">
                    </div>
                    <div class="form-group">
                        <label>Home page</label>
                        <input type="text" name="website" class="form-control" style="width: 300px;">
                    </div>
                    <div class="form-group">
                        <label>Content</label>
                        <textarea name="content" id="newFormContent" class="form-control" rows="10" required="required"></textarea>
                    </div>
                    <button type="submit" class="btn btn-lg btn-success col-lg-12">Submit</button>
                </form>
            </div>

            <script>
            function reply(a) {
              var nickname = a.parentNode.parentNode.firstChild.nextSibling.getAttribute('data');
              var textArea = document.getElementById('newFormContent');
              textArea.innerHTML = '@'+nickname+' ';
            }
            </script>

            <div class="conmments" style="margin-top: 100px;">
                @foreach ($article->hasManyComments as $comment)

                    <div class="one" style="border-top: solid 20px #efefef; padding: 5px 20px;">
                        <div class="nickname" data="{{ $comment->nickname }}">
                            @if ($comment->website)
                                <a href="{{ $comment->website }}">
                                    <h3>{{ $comment->nickname }}</h3>
                                </a>
                            @else
                                <h3>{{ $comment->nickname }}</h3>
                            @endif
                            <h6>{{ $comment->created_at }}</h6>
                        </div>
                        <div class="content">
                            <p style="padding: 20px;">
                                {{ $comment->content }}
                            </p>
                        </div>
                        <div class="reply" style="text-align: right; padding: 5px;">
                            <a href="#new" onclick="reply(this);">回复</a>
                        </div>
                    </div>

                @endforeach
            </div>
        </div>

    </div>

</body>
</html>
```

#### 建立評論儲存功能
* 建立controller
`php artisan make:controller CommentController`
* 建立route
`Route::post('comment', 'CommentController@store');`
* 增加 store 函數
```php=
public function store(Request $request)
{
    if (Comment::create($request->all())) {
        return redirect()->back();
    } else {
        return redirect()->back()->withInput()->withErrors('评论发表失败！');
    }
}
```
* 批量賦值
採用批量賦值方法來減少儲存評論的代碼
model可利用「create」方法將array型態的資料進行模型(實體)的新增，而一般的「save」方法僅能進行單一屬性(欄位)設定，例如：
```php=
// create方法
$data = [
    'column_1' => '...',
    'column_2' => '...',
];
Post::create($data);

// save方法
$post = new Post;
$post->column_1 = '...';
$post->column_2 = '...';
$post->save();
```