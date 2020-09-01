---
title: Laravel Middleware
date: 2019-10-27 15:51:08
tags: 
    - Laravel
    - Middleware
categories: Laravel
---
## Middleware

<!--more-->
***

* Middleware提供了一種方便的機制來過濾進入應用程序的 HTTP 請求。
* 最簡單的使用方法就是比對使用者的請求資料是不是符合我們的要求，如果符合，就繼續下一個個程序；如果不符合，可以導向另外一個頁面 or 回傳一個錯誤訊息。
* 假設我們有<font color="red">重複要驗證</font>的資料，我們可以寫在 Middleware，不用每次在 Controller 中各 function 執行的時候都要重複寫。

## Start using middleware
`php artisan make:middleware filename`
* Laravel 預設和新創建的 middleware 都存放在 app/Http/Middleware 裡.

## Before Middleware
* Before Middleware 我翻譯成「前行中介層」，就是先執行中介層內容，再丟給下一個接收的人處理。
```php= 
<?php

namespace App\Http\Middleware;

use Closure;

class BeforeMiddleware
{
    public function handle($request, Closure $next)
    {
        // Perform action       //先執行任務  
        echo 'before'.'<br/>';
        return $next($request); //這裡才處理 request
    }
}
```

## After Middleware
* 為「後行中介層」，就是先請下一個接收 request 的人先執行，執行完在回頭執行中介層內容。
```php=
<?php

namespace App\Http\Middleware;

use Closure;

class AfterMiddleware
{
    public function handle($request, Closure $next)
    {
        $response = $next($request); //先處理 request （next 本身是閉包，會先處理 request)
        // Perform action			 //這裡才執行任務
        echo '</br>'.'after';
        return $response;
    }
}
```

## Registering Middleware
### 全域使用(Global Middleware)
* 希望在對應用程序的每個 HTTP request 期間運行中介層，請在`app/Http/Kernel.php ` class的`middleware`屬性中列出中介層類。
```php=
<?php

namespace App\Http;

use Illuminate\Foundation\Http\Kernel as HttpKernel;

class Kernel extends HttpKernel
{
    protected $middleware = [
        \App\Http\Middleware\CheckForMaintenanceMode::class,
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
        \App\Http\Middleware\TrimStrings::class,
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
        \App\Http\Middleware\TrustProxies::class,
        
        //這裡可以把我們新建立的 BeforeMiddleware 加入
        \App\Http\Middleware\BeforeMiddleware::class,

    ];
    
}
```

* 請注意，一但設定為全域使用，那麼任何請求進來一定都要先經過這個中介層處理，所以使用上要特別小心，否則萬一中介層出現 bug，你又忘記自己設定了全域中介層，那...這個 bug 就有得找了！

### 為 Route 指派 Middleware
* 如果要分配中介層給特定的 route，可以在 `app/Http/Kernel.php` 中的 `$routeMiddleware` 屬性，自定義中介層的 key name
```php=
<?php

namespace App\Http;

use Illuminate\Foundation\Http\Kernel as HttpKernel;

class Kernel extends HttpKernel
{
    protected $routeMiddleware = [
        'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    ];
}

```

#### 使用方法：
一旦定義了中介層的 key name，在 route 中就可以使用以下方法直接調用

##### 一個中介層：
* 方法一：
```php=
Route::get('admin/profile', function () {
    //
})->middleware('auth');
```
* 方法二：
```php=
Route::middleware('auth')->get('admin/profile', function () {
    //
});
```
方法二比較符合 Laravel 的執行流程，因為流程是先經過中介層，再進到 Route 裡的 function。
* 方法三：
你也可以使用完整 class name
PS：完整的意思就是要列出「路徑 + class name」
```php=
use App\Http\Middleware\CheckAge; //路徑，最終停在一個 CheckAge.php 的檔案

Route::get('admin/profile', function () {
    //
})->middleware(CheckAge::class); // CheckAge.php 中有一個 CheckAge 的 class name

```

##### 多個中介層：
也可以同時調用好幾個 middleware (當然要先定義好 key 值)
```php=
Route::middleware('first', 'second')->get('/', function () {
    //
});

```

### Middleware 群組化
* 有時候為了方便重複調用多個中介層，就可以把好幾個中介層，群組在一個 key name 之中，只要定義在 `app/Http/Kernel.php` 中的 `$middlewareGroups` 屬性
* Ex:
```php=
/**
 * The application's route middleware groups.
 *
 * @var array
 */
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

    'api' => [
        'throttle:60,1',
        'auth:api',
    ],
];
```
* 可以使用與使用「單一中介層」類似的方法將 middleware group 分配給 route 和 controller。
* Ex:
```php=
//方法一：
Route::get('/', function () {
    //
})->middleware('web');

//方法二：
Route::group(['middleware' => ['web']], function () {
    //
});
```

#### 官方文件小重點：
> Out of the box, the web middleware group is automatically applied to your routes/web.php file by the RouteServiceProvider.
官方文件這句話就是如果我們使用 web.php 來設定 route 的話，那 Laravel 內建就會調用 'web' 這個 middleware group!

### Middleware Parameters
* 中介層是可以接受額外的變數。加入的方法就是在中介層裡面的 handle 方法中加入第三個參數。
* 也就是在 $next 參數之後，額外新增一個參數。
* Ex:
```php=
<?php

namespace App\Http\Middleware;

use Closure;

class CheckRole
{
    /**
     * Handle the incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @param  string  $role
     * @return mixed
     */
    public function handle($request, Closure $next, $role)
    {
        if (! $request->user()->hasRole($role)) {
            // Redirect...
        }

        return $next($request);
    }

}
```
* 使用方法：
可以在定義 route 時，透過 : 分隔 middleware name 和 middleware parameters

* Ex:
```php=
Route::put('post/{id}', function ($id) {
    //
})->middleware('role:editor');  //role 就是 middleware key name，
				 //editor 就是 middleware parameter
```

* ps:如果有多個參數，就以「,」分隔
```php=
//middleware 設定
    public function handle($request, Closure $next, $greet, $name)
    {
        echo $greet.', ';
        echo $name.'</br>';
        return $next($request);
    }
```

* PS:記得要去 `app/Http/Kernel.php` 定義中介層的 key name。我的範例是取名為 'before'
```php= 
//route 設定
    Route::middleware('before:Hello,Kao')->get('/before', function (){
        //
    });
```
* 如此一來
before 為中介層的 key name
Hello 為中介層中 handle 中的參數 $greet
Kao 為 handle 中的參數 $name

### Before Middleware VS After Middleware
1. 我們延用上面的 Before Middleware 和 After Middleware 的範例程式。
2. `app/Http/Kernel.php` 也定義了中介層的 key name
```php= 
 protected $routeMiddleware = [
        'before' => \App\Http\Middleware\BeforeMiddleware::class,
        'after' => \App\Http\Middleware\AfterMiddleware::class,
    ];
```

3. 接著我們 route 設定如下
```php=
Route::middleware('before')->get('/before', function (){
    echo 'in Route';
});

Route::middleware('after')->get('/after', function (){
    echo 'in Route';
});

```

* 實際跑跑看

* 這裡就可以看到差別，因為我們故意將 route 設定成只執行印出 in Route 的動作，所以我們可以得知 Before Middleware 先執行自己的內容，在執行 route 的內容，而反過來的 After Middleware 則是先執行 route 的內容，才回去執行自己的內容。
* 這裡的關鍵就是 $next 這個參數是在'前'還是'後'，由於 $next 本身是一個 closure 所以會執行一個動作就是把 $request 當成參數交給下一個程式執行，而那一位「下一個程式」這裡先不探討是誰，我只知道它都會執行 route 裡的 function!最終，等「下一個程式」也跑完了，程序就會回到 $next 調用之後，接著執行 $next 之後的程式。