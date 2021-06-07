---
title: "PHP Unit"
date: "2021-06-08"
author: "PHP Unit"
summary: "介紹用laravel使用PHP Unit"
---

#### 介紹

laravel在寫單位測試時，常常會用到第三方API來做測試，會需要用到Mock，用Mock並不是真的要去執行API，而是要模擬執行API。

#### 流程圖



#### 指令

1. 建立測試檔案，例如：BasicTest

   ```bash
   php artisan make:test BasicTest 新增到test/Feature資料夾裡
   php artisan make:test BasicTest --unit  要補上unit才會新增到Unit資料夾裡
   ```

2. 測試全部class

   ```bash
   ./vendor/bin/phpunit 這個指令會顯示很詳細的資訊
   php artisan test 顯示簡化的資訊
   ```
3. 測試Feature或Unit全部的class

   ```bash
   php artisan test --testsuite=Feature
   php artisan test --testsuite=Unit
   ```

4. 測試某個class，例如：測試Unit/BasicTest

   ```bash
    php artisan test --testsuite=Unit --filter=BasicTest
   ```


laravel在寫單位測試時，常常會用到第三方API來做測試，會需要用到Mock，用Mock並不是真的要去執行API，而是要模擬執行API。

#### 流程圖

![mock](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/mock.png>)

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
 	public function testNewOrder()
    {
        $data = [
            "invoice1", "invoice2"
        ];

        $mock = $this->initMock(InvoiceService::class);
        $mock->shouldReceive('newInvoice')
            ->once()
            ->with($data)
            ->andReturn('成功');

        $test = $mock->newInvoice($data);
    }
}
```

以下介紹使用的Mock functuion

- shouldReceive：呼叫類別的function做Mock，這裡呼叫InvoiceService的newInvoice()。
- once：只執行一次。
- with：使用參數。
- andReturn：新增回傳的參數。

我們會先用建立class使用Mock物件，在執行測試的function

例如：InvoiceService要使用newInvoice之前，要將建立Invoice的Mock物件，

```php
class InvoiceService
{
	public function __construct(Invoice $invoice)
    {
        $this->invoice = $invoice
    }
    
    public function newInvoice(Order $order)
    {
        $now = array_push($data, "invoiceTest");
        return $now;
    }
}
```

```php
class InvoiceServiceTest extends TestCase
{
    public function testRunNewInvoice()
    {
        $mock = $this->initMock(Invoice::class);
        $mock->shouldReceive('newInvoice')->once();
        $test = $mock->newInvoice();
    }
}
```

#### 問題

- Mock 原理是什麼(要推論怎麼使用Mock?)：

- Mock 的用途：可以替我們執行function，但不是真的在執行

- Mock做了哪些事：將我們要測試的function，包裝成Mock之後，在執行測試

- 為什麼Mock可以代替 ：例如我們想要在測試DB資料，可以用Mock幫我們做，但是不會把資料寫入DB
  (為什麼Mock可以不真的去執行資料寫入DB)

- Mock產生出來的結果是什麼：會建立測試function的Mock物件，去模擬執行測試function


#### 錯誤

- 如果只建立Mock物件，沒指向function會顯示錯誤：
   Method newInvoice(....) from Mockery_0_App_Services_InvoiceService should be called exactly 1 times but called 0 times.

  代表建立一個Mock物件，執行0次，以上面的範例少寫$test = $mock->newInvoice()，會顯示錯誤。

參考資料：https://dustinhsiao21.github.io/laravel/use-mock-in-laravel-phpunit/、https://learnku.com/docs/laravel/5.8/mocking/3941、https://laravel.com/docs/8.x/mocking







