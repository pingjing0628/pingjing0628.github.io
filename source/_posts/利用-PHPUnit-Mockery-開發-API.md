---
title: 利用 PHPUnit + Mockery 開發 API
date: 2019-11-03 23:21:47
tags:
	- PHP
	- phpunit
	- mockery
categories:
	- PHP
---
## Mockery

<!---more-->
***

### 先建立一個服務
* 先於 `app`底下建立一個 `Services`資料夾

### 建立測試資料
* 於 `Tests\Unit` 底下建立一個 `Services`資料夾，在建立一個 `ClientTest.php`的檔案
* 建立一個 `test_query()`的function
* function 內建立連線且設定需送出與回傳的值
```php= 
$guzzleClient->shouldReceive('request')->andReturn(
        new Response(200, [], file_get_contents(__DIR__.'/result.txt'))
    );

$client = new Client($guzzleClient, $log, $key); 

```
* 確認建立連線後，檢視所需得到的資料格式，在建立 `ClientTest.php`的檔案同層建立 `result.txt`假資料
* 而後於檔案上方引入，並使用
```php=
use Mockery as m;
```
```php=
$guzzleClient = m::mock(GuzzleClient::class);
```
* 最後放入測試資料的格式驗證
```php=
$this->assertArraySubset([
    'price' => 9165.74788036,
    'volume_24h' => 15782991288.054,
    'percent_change_1h' => -0.537992,
    'percent_change_24h' => -1.19578,
    'percent_change_7d' => 16.4927,
    'market_cap' => 162835578819.6922,
    'last_updated' => '2019-06-18T12:14:22.000Z',
], $client->query('btC')); 
```

### 回到服務資料夾內建立連線檔案
* 建立連線的 `Client` 檔案，使用GuzzleHttp連線，並於建構子中依賴注入
* 建立 `query()`透過key去發出 `request` ，並將取回的資料進行 `json_decode()`，於最後透過laravel collection的方法，filter 出符合的值
```php=
public function __construct(GuzzleClient $client)
{
    $this->client = $client;
}
    
public function query($symbol = 'BTC')
    
    $response = $this->client->request('GET','https://pro-api.coinmarketcap.com/v1/cryptocurrency/listings/latest', [
        'headers' => [
            'X-CMC_PRO_API_KEY' => 'xxxxxxx',
        ],
    ]);

    $results = json_decode($response->getBody()->getContents(), true);

    return Arr::get(collect($results['data'])->filter(function ($item) use ($symbol) {
        return $item['symbol'] === trim(strtoupper($symbol));
    })->first(), 'quote.USD');
}
```

### 想要紀錄呼叫api次數
* 由於紀錄log是呼出去，因此需使用 `spy`去監測紀錄
* 於服務資料夾下建立 `Log.php`檔案，並建立 `info()`function
```php=
<?php
namespace App\Services;

class Log
{
    public function info()
    {

    }   
}
```

* 在測試檔案中加上log , 也於連線方法上引入
```php=
$log = m::spy(Log::class);
$client = new Client($guzzleClient, $log);	 
```

* 需在驗證連線上加上 `once()`
```php=
$guzzleClient->shouldReceive('request')->andReturn(
    new Response(200, [], file_get_contents(__DIR__.'/result.txt'))
)->once();
```

* 於測試程式最後加上
```php=
$log->shouldHaveReceived('info')->with('xxxxxxxx')->once();
```
* 最後需在此方法之上加入 `teardown()` function
* 此意思是代表 在每個測試後執行，另外也可以只在整個測試類別執行一次的
```php=
protected function tearDown():void
{
    parent::tearDown();
    m::close();
}
```

* 最後在連線的建構子上加上
```php=
public function __construct(GuzzleClient $client, Log $log)
{
    $this->client = $client;
    $this->log = $log;
}
```

* query()加上
```php=
$this->log->info('xxxxxxxx');
```

### 加上環境檔的key，使用依賴注入
* 於 `Providers`資料夾內的 `AppServiceProvider`加上
```php=
public function register()
{
    $this->app->singleton(Client::class, function() {
        return new Client(new GuzzleClient, new Log(), env('CMC_API_KEY'));
    });
}
```

* 連線檔案的建構子加上key依賴注入
```php=
public function __construct(GuzzleClient $client, Log $log, $key = null)
{
    $this->client = $client;
    $this->log = $log;
    $this->key = $key;
}
```

* query()內也更改key的引入
```php=
$this->log->info($this->key);

$response = $this->client->request('GET','https://pro-api.coinmarketcap.com/v1/cryptocurrency/listings/latest', [
    'headers' => [
        'X-CMC_PRO_API_KEY' => $this->key,
    ],
]);
```

* 測試檔案key的更改
```php=
public function test_query() //this test failed
{
    $key = 'xxx';
    $guzzleClient = m::mock(GuzzleClient::class);
    $guzzleClient->shouldReceive('request')->andReturn(
        new Response(200, [], file_get_contents(__DIR__.'/result.txt'))
    )->once();

    $log = m::spy(Log::class);
    $client = new Client($guzzleClient, $log, $key); 
    // assertions

    $this->assertArraySubset([
        'price' => 9165.74788036,
        'volume_24h' => 15782991288.054,
        'percent_change_1h' => -0.537992,
        'percent_change_24h' => -1.19578,
        'percent_change_7d' => 16.4927,
        'market_cap' => 162835578819.6922,
        'last_updated' => '2019-06-18T12:14:22.000Z',
    ], $client->query('btC')); 

    $log->shouldHaveReceived('info')->with($key)->once();
}
```


### 為何要使用測試
* 寫測試不是為了驗證結果，而是為了驗證過程
* 要用什麼測試，假資料或是等等等，須自行摸索
