## PHPUnit+Morkery 實做API

#### 目錄

- 介紹PHPUnit+Mockery
- API說明
- 怎麼測試?
- 實做範例-驗證測試結果
- 如果spy改用mock會發生什麼?

#### 介紹PHPUnit+Mockery

PHPUnit可以在寫程式碼之前，釐清程式流程，主要以[TDD](https://tw.alphacamp.co/blog/tdd-test-driven-development-example) 5個步驟來寫測試，Mockery是模擬使用API做回傳資料，來確認跟自己設的假資料有沒有一樣

#### API說明

以下範例使用的API是用比特畢轉換成美元的資料，使用API之前要先申請帳號取得key

1. 申請帳號

   用[API網址](https://pro.coinmarketcap.com/)去註冊後取得key

   ![phpunit+mockery1](C:\xampp\htdocs\markdown_note\assets\images\phpunit+mockery1.PNG)

2. 將API Key寫到程式裡

   ```php
   $key = '216c9611-9a19-44cd-9cc2-052d9fbcd761';
   ```

去API網站的docmentation，可以看到API回傳哪些資料

![phpunit+mockery2](C:\xampp\htdocs\markdown_note\assets\images\phpunit+mockery2.PNG)

#### 怎麼測試?

要驗證用API回傳的資料，有沒有跟預期的一樣，所以要先準備好一個預期的資料

```php
$test = json_encode([
    'price' => 9165.74788036,
    'volume_24h' => 15782991288.054,
    'percent_change_1h' => -0.537992,
    'percent_change_24h' => -1.19578,
    'percent_change_7d' => 16.4927,
    'market_cap' => 162835578819.6922,
    'last_updated' => '2019-06-18T12:14:22.000Z',
]);
```

在透過測試程式模擬執行API回傳的資料，是不是跟準備好的資料一樣

#### 實做範例

以下示範用PHPUnit+Morkery 模擬用API回傳資料

```php
Tests\Unit\Services\test_query

use Mockery as m;
use App\Services\Log;
use App\Services\Client;
use GuzzleHttp\Psr7\Response;
use PHPUnit\Framework\TestCase;
use GuzzleHttp\Client as GuzzleClient;

public function test_query()
    {
        $key = '216c9611-9a19-44cd-9cc2-052d9fbcd761';

        //Arrange
        //建立一個代替mock的GuzzleClient來做驗證
        $guzzleClient = m::spy(new GuzzleClient);
        //$guzzleClient = m::mock(new GuzzleClient);

        //呼叫request()，回傳Response物件，內容會跟result.txt一樣，只執行一次
        $guzzleClient->shouldReceive('request')->andReturn(
            new Response('200', [], file_get_contents(__DIR__.'/result.txt'))
        )->once();

        //Act
        //建立一個代替mock的log class來做驗證
        $log = m::spy(Log::class);
        //$log = m::mock(Log::class);

       //建立Services/Client的物件，參數帶guzzleClient、log、key
        $client = new Client($guzzleClient, $log, $key);

        //client物件執行query()，參數帶BTC並且用json_encode，將回傳的array轉成String
        $json = json_encode($client->query('BTC'));

        //建立test變數將自己設的假的array資料，用json_encode轉成String
        $test = json_encode([
            'price' => 9165.74788036,
            'volume_24h' => 15782991288.054,
            'percent_change_1h' => -0.537992,
            'percent_change_24h' => -1.19578,
            'percent_change_7d' => 16.4927,
            'market_cap' => 162835578819.6922,
            'last_updated' => '2019-06-18T12:14:22.000Z',
        ]);

        //Assert
        //將client物件跟自己設定假資料做比對，確認內容有沒有一樣
        $this->assertEquals($json, $test);

        //驗證Services的Log有沒有info 這個function，帶key參數，並且執行一次
        $log->shouldHaveReceived('info')->with($key)->once();
    }
```

先用[API網址](https://pro.coinmarketcap.com/)註冊取得key，在用變數去設定，在建立一個spy的GuzzleClient物件來做驗證，再去呼叫`request()`，並且設定回傳的Response內容，要跟result.txt的一樣，這裡用GuzzleClient物件使用API，執行`query()`，拿到回傳的Response的資料轉成String，在用自己設定的假資料做比對，確認內容有沒有一樣。log只確認有沒有使用info這個function。

#### 如果spy改用mock會發生什麼?

```php
$guzzleClient = m::spy(new GuzzleClient);
->$guzzleClient = m::mock(new GuzzleClient);

$log = m::spy(Log::class);
->$log = m::mock(Log::class);
```

guzzleClient並沒有產生錯誤，因為一樣去呼叫`request()`建立物件，如果guzzleClient有function需要回傳的話會變成null，但是用spy會有資料回傳，會真的呼叫function，spy的目的是驗證呼叫的function，有沒有真的被執行，mock則是模擬的資料，是否跟預期的資料符合。

log會產生錯誤，mock並沒有`shouldHaveReceived()`，要改成`shouldReceive()`，去呼叫info

參考資料：

- https://github.com/recca0120/fight-editor
- https://pro.coinmarketcap.com/
- https://www.jyt0532.com/2018/01/04/test-double-spy/
