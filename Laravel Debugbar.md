---
title: "laravel debugber"
date: "2021-05-09"
author: "debugber"
summary: "說明使用laravel debugber 做debugber"
categories: ["debugger"]
tags: ["laravel debugber","debugger"]
---

### 1.安裝**[debugbar](https://github.com/barryvdh/laravel-debugbar)**套件

用composer 安裝

```javascript
$ composer require barryvdh/laravel-debugbar --dev
```

## 2.設定Config

如果想自動執行，在config/app.php的providers，新增下面這行

```php
Barryvdh\Debugbar\ServiceProvider::class,
```

在config/app.php的aliases，新增下面這行

```php
'Debugbar' => Barryvdh\Debugbar\Facade::class,
```

在執行以下這行指令，config會新增debugbar.php

```bash
php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
```

### 3.範例

使用Debugbar，在controller一定要return到view，不然會看不到自己寫的Debugbar內容

可以用錯誤、等待、訊息、時間等等來顯示，

```php
Debugbar::info($object);
Debugbar::error('Error!');
Debugbar::warning('Watch out…');
Debugbar::addMessage('Another message', 'mylabel');

Debugbar::startMeasure('render','Time for rendering');
Debugbar::stopMeasure('render');
Debugbar::addMeasure('now', LARAVEL_START, microtime(true));
Debugbar::measure('My long operation', function() {
    // Do something…
});
```

可以記錄拋錯的訊息

```php
try {
    throw new Exception('foobar');
} catch (Exception $e) {
    Debugbar::addThrowable($e);
}
```

用多個變數或集合來記錄

```php
debug($var1, $someString, $intValue, $object);
collect([$var1, $someString])->debug();
```

可以記錄SQL查詢

```php
$pdo = new DebugBar\DataCollector\PDO\TraceablePDO(new PDO('sqlite::memory:'));
$debugbar->addCollector(new DebugBar\DataCollector\PDO\PDOCollector($pdo));
$pdoCollector = new DebugBar\DataCollector\PDO\PDOCollector();
$pdoCollector->addConnection($pdoRead, 'read-db');
$pdoCollector->addConnection($pdoWrite, 'write-db');

$debugbar->addCollector($pdoCollector);
```

請求資料

```php
$debugbar->addCollector(new DebugBar\DataCollector\RequestDataCollector());
```

#### clockwork

laravel debugbar 有使用clockwork，例如：使用chrome的clockwork插件

```php
  if ($app['config']->get('debugbar.clockwork') && ! $this->hasCollector('clockwork')) {

            try {
                $this->addCollector(new ClockworkCollector($request, $response, $sessionManager));
            } catch (\Exception $e) {
                $this->addThrowable(
                    new Exception(
                        'Cannot add ClockworkCollector to Laravel Debugbar: ' . $e->getMessage(),
                        $e->getCode(),
                        $e
                    )
                );
            }

            $this->addClockworkHeaders($response);
        }
```

#### Javascript

可以用javascript來實做，在js檔案裡建立

```javascript
var debugbar = new PhpDebugBar.DebugBar();
debugbar.createTab("messages", new PhpDebugBar.Widgets.MessagesWidget());
debugbar.createIndicator("time", "cogs", "Request duration");
```

與其他套件比較





參考資料:

https://github.com/itsgoingd/clockwork、https://chrome.google.com/webstore/detail/clockwork/dmggabnehkmmfmdffgajcflpdjlnoemp?hl=zh-TW、https://blog.csdn.net/tanmx219/article/details/78915890

