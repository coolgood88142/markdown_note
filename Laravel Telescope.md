---
title: "Telescope"
date: "2021-05-09"
author: "ClockWork"
summary: "說明使用ClockWork 做debugger"
categories: ["debugger"]
tags: ["telescope","debugger"]
---

**telescope**  。

### 1.安裝**[Telescope](https://github.com/itsgoingd/clockwork)**套件

用composer 安裝

```javascript
$ composer require laravel/telescope
```

安裝之前，先在確認好laravel的版本，必須要laravel 8才可以安裝

### 2.安裝telescope

安裝好之後，在laravel安裝telescope

```javascript
php artisan telescope:install
```

如果後續要更新，執行以下的指令

```
php artisan telescope:publish
php artisan migrate
```


## 3.設定Provider 

安裝好之後，在config\app.php，移除以下這行

```
App\Providers\TelescopeServiceProvider::class
```

在App\Providers\AppServiceProvider，新增以下這段程式碼

```php
public function register()
{
    if ($this->app->environment('local')) {
        $this->app->register(\Laravel\Telescope\TelescopeServiceProvider::class);
        $this->app->register(TelescopeServiceProvider::class);
    }
}
```

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

可以用laravel的排程，去執行telescope:prune，來記錄資料

```php
$schedule->command('telescope:prune')->daily();
```

laravel telescope預設24小時後，會自動刪除記錄，可以設定每多少個小時做刪除，例如：48小時

```php
$schedule->command('telescope:prune --hours=48')->daily();
```



參考資料:

https://github.com/itsgoingd/clockwork、https://chrome.google.com/webstore/detail/clockwork/dmggabnehkmmfmdffgajcflpdjlnoemp?hl=zh-TW

