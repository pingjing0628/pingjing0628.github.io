---
title: Laravel CSRF
date: 2019-10-27 15:50:28
tags: 
	- Laravel
	- CSRF
categories: Laravel
---
## CSRF

<!--more-->
***

* 跨站請求偽造（英語：Cross-site request forgery），也被稱為 one-click attack 或者 session riding，通常縮寫為 CSRF 或者 XSRF，是一種挾制用戶在當前已登錄的Web應用程式上執行非本意的操作的攻擊方法。
* 跟跨網站指令碼（XSS）相比，XSS 利用的是用戶對指定網站的信任，CSRF 利用的是網站對用戶網頁瀏覽器的信任。

## Laravel 內建 CSRF token
* Laravel 透過應用程式自動產生一個 CSRF「token」來管理每個活躍的使用者 session。這個 token 用於驗證已認證使用者是否實際向應用程式發出請求。

1. `vender/laravel/framework/src/Illuminate/Session/Store.php` 這支檔案，可以知道每次進入 laravel 專案的時候，都會檢查 session 中 `_token` 是否存在，如果不存在就會呼叫 `regenerateToken` 重新生成一個 token
```php= 
  public function start()
    {
        $this->loadSession();

        if (! $this->has('_token')) {
            $this->regenerateToken();
        }

        return $this->started = true;
    }
```

2. `regenerateToken` 實作內容，即隨機產生亂數字元。
```php=
public function regenerateToken()
{
    $this->put('_token', Str::random(40));
}
```
3. 實際用 POSTMAN 看看伺服器是不是真的有回傳 token
![](https://i.imgur.com/T6PLbma.png)

4. 確實有一個名為 XSRF-TOKEN 的 token。

## 添加校驗 token