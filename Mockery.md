---
title: "MOCKERY"
date: "2021-06-06"
author: "laravel MOCKERY"
summary: "介紹使用MOCKERY"
---



laravel在寫單位測試時，常常會用到第三方API來做測試，會需要用到Mock，用Mock並不是真的要去執行API，而是要模擬執行API。

我們寫function測試，一般我們會在TestCase寫成繼承，讓每個測試檔案都可以直接用instance(class)，執行Mock。

```php
class TestCase extends BaseTestCase
{
    use CreatesApplication;

     /**
     * 初始化mock物件
     *
     * @param string $class
     * @return Mockery
     */
    protected function initMock($class)
    {
        $mock = \Mockery::mock($class);
        $this->app->instance($class, $mock);

        return $mock;
    }
}
```

以下示範，用OrderServiceTest的InvoiceService newInvoice()，來建立Moke物件，之後在執行測試

```php
<?php

class OrderServiceTest extends TestCase
{
    public function setUp()
    {
        parent::setUp();
        $this->invoice = $this->initMock(InvoiceService::class);
    }

	//....
    public function testNewOrder()
    {
        //arrange
        $invoiceReturn = [
            'invoice_number' => 'xxx'
            //...
        ];
        //...

        $data = [
            'invoice_number' => '110',
            'name' => 'ming',
        ];
            
        $this->mock->shouldReceive('newInvoice')
            		->once()
            		->with($order)
            		->andReturn($invoiceReturn);
        $result = $this->invoice->newInvoice($order);
    }
}
```

以下介紹使用的Mock functuion

- shouldReceive：呼叫類別的function做Mock，這裡呼叫InvoiceService的newInvoice()。
- once：只執行一次。
- with：使用參數。
- andReturn：新增回傳的參數。

在使用Mock必須要在測試的物件，先做執行可以用，我們會先用建立class使用Mock物件，在執行測試

例如：InvoiceService要使用newInvoice、formatOrder等等function 做測試前，要先執行InvoiceServiceTest 的setup，將建立Invoice的Mock物件，

```php
class InvoiceService
{
	public function __construct(Invoice $invoice)
    {
        $this->invoice = $invoice
    }
    
    public function newInvoice(Order $order)
    {
        $invoiceData = $this->formatOrder($order);
        return $this->invoice->invoice($invoiceData);
    }
    
    private function formatOrder(Order $order)
    {
        //...
    }
}
```

```php
class InvoiceServiceTest extends TestCase
{
    public function setUp()
    {
        $this->mock = $this->initMock(Invoice::class);
        $this->service = app(InvoiceService::class);
    }
}
```

結論：使用Mock做測試之前，都要先建立好已經有Mock的Class物件

問題：

- Mock 原理是什麼：將測試的function寫成instance，並且class去實做function
- Mock 的用途：可以替我們執行function，但不是真的在執行
- Mock做了哪些事：將我們要測試的function，包裝成Mock之後，在執行測試
- 為什麼Mock可以代替 ：例如我們想要在測試DB資料，可以用Mock幫我們做，但是不會把資料寫入DB
- Mock產生出來的結果是什麼：會建立測試function的Mock物件，去模擬執行測試function



參考資料：https://dustinhsiao21.github.io/laravel/use-mock-in-laravel-phpunit/、https://learnku.com/docs/laravel/5.8/mocking/3941、https://laravel.com/docs/8.x/mocking







