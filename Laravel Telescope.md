---
title: "Telescope"
date: "2021-05-09"
author: "telescope"
summary: "說明使用laravel telescope 做debugger"
categories: ["debugger"]
tags: ["laravel telescope","debugger"]
---

laravel telescope可以用多個Watcher，有Requests、Exceptions、Logs等等可以看到，各種相關的資料

### 1.安裝**[Telescope](https://github.com/itsgoingd/clockwork)**套件

用composer 安裝

```javascript
$ composer require laravel/telescope
//如果要本機安裝要使用
$ composer require laravel/telescope --dev
```

安裝之前，先在確認好laravel的版本，必須要laravel 8才可以安裝

### 2.用laravel安裝

在laravel安裝telescope

```javascript
php artisan telescope:install
```

安裝時會顯示錯誤

```bash
There are no commands defined in the "telescope" namespace.
```

先移除composer.json裡的laravel/telescope，在執行composer dump-auto，之後在做一次

如果有需要搬移資料庫，在執行這行

```
php artisan migrate
```

## 3.本機安裝

在App\Providers\TelescopeServiceProvider，移除register全部的內容，之後在AppServiceProvider，新增以下這段程式碼

```php
public function register()
{
    if ($this->app->environment('local')) {
        $this->app->register(TelescopeServiceProvider::class);
    }
}
```

最後在composer.json，新增以下這行

```javascript
"extra": {
    "laravel": {
        "dont-discover": [
            "laravel/telescope"
        ]
    }
},
```

如果不想安裝後，不想使用telescope，可以在config/telescope.php，新增以下這行

```php
'enabled' => env('TELESCOPE_ENABLED', true),
```

啟用telescope會快速記錄所有資料，可以利用laravel的Task Scheduling，減緩記錄資料

```php
$schedule->command('telescope:prune')->daily();
```

預設的情況下，會自動在24小時之後刪除記錄，可以設定時間保留記錄，例如：48小時

```php
$schedule->command('telescope:prune --hours=48')->daily();
```

## 4.設定Provider

將app/Providers/TelescopeServiceProvider.php的gate()，調整以下的程式碼

```php
protected function gate()
{
    Gate::define('viewTelescope', function ($user) {
        return in_array($user->email, [
            'user@email.com',
        ]);
    });
}
```

限制設定使用者ID，使用Telescope

```php
return in_array($user->id, [
    1, 2, 3,
]);
```

#### 升級Telescope版本

除了執行php artisan telescope:publish，還要再composer.json，新增以下這行

```javascript
{
    "scripts": {
        "post-update-cmd": [
            "@php artisan telescope:publish --ansi"
        ]
    }
}
```

#### Watchers

在config/telescope.php，可以設定Watchers要收集哪些資料

```php
'watchers' => [
    Watchers\CacheWatcher::class => true,
    Watchers\CommandWatcher::class => true,
    ...
],
```

或者可以設定要記錄哪個model的事件，例如created、updated

```php
'watchers' => [
    Watchers\ModelWatcher::class => [
        'enabled' => env('TELESCOPE_MODEL_WATCHER', true),
        'events' => ['eloquent.created*', 'eloquent.updated*'],
    ],
    ...
],
```

查詢SQL時，可以記錄小於多少查詢時間的資料，例如小於50秒

```php
'watchers' => [
    Watchers\QueryWatcher::class => [
        'enabled' => env('TELESCOPE_QUERY_WATCHER', true),
        'slow' => 50,
    ],
    ...
],
```

設定請求資料時，限制收集多少筆記錄，例如50筆

```php
'watchers' => [
    Watchers\RequestWatcher::class => [
        'enabled' => env('TELESCOPE_REQUEST_WATCHER', true),
        'size_limit' => env('TELESCOPE_RESPONSE_SIZE_LIMIT', 50),
    ],
    ...
],
```

參考資料:

https://laravel.com/docs/8.x/telescope、https://github.com/laravel/telescope/issues/954、https://hoohoo.top/blog/laravel-telescope/
