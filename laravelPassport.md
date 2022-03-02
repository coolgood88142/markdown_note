# laravel Passport

### 大綱

- 什麼是laravel Passport
- 什麼是OAuth 2.0
- 安裝步驟
- 範例
- 參考資料

### 什麼是laravel Passport

以前的身分驗證的方法是，透過傳統登入表單，在用API來驗證身分，並且要保持登入狀態，現在laravel 提供Laravel Passport讓API身份驗證變得更容易，Laravel Passport利用OAuth 2.0。

使用者在登入後，驗證完帳密後，系統會提供Access Token(授權碼)、Scope(所需要的資源清單)，在告知是否授權給系統，若同意的話，系統拿到Access Token後，會依照Scope提供的資訊，回傳使用者資料

使用者資料會有userID、姓名、大頭照等等，但是Scope則是會告知系統，我只會拿使用者的userID、姓名，並不會拿該使用者的全部資料

### 什麼是OAuth

OAuth是一種授權的通訊協定，可向應用程式請求授權，取得資料的存取權，透過存取的Token獲得授權，並且用Token拿到資料

### 安裝步驟

#### 安裝套件

```bash
composer require laravel/passport
```

#### 設定config/app.php

providers中新增這行

```bash
Laravel\Passport\PassportServiceProvider::class
```

#### 資料庫遷移

```bash
php artisan migrate
```

#### 執行passport

```bash
php artisan passport:install
```

### 範例

在登入畫面向系統，授權資料存取

#### 1.User Model新增檢查身份驗證機制

App/User.php新增HasApiTokens

```php
<?php
 
namespace App;
 
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Passport\HasApiTokens;
 
class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
}
```

#### 2.AuthServiceProvider 加入Passport

新增Passport::routes()

```php
class AuthServiceProvider extends ServiceProvider
{
    ...
	public function boot()
    {
        $this->registerPolicies();
 
        Passport::routes();
    }
}
```

#### 3.修改config/auth.php

```php
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],
 
    'api' => [
        'driver' => 'passport',
        'provider' => 'users',
    ],
],
```

#### 4.發布 Passport Vue

passport有內建的vue component，要用之前，請先安裝好Vue之後，在做這個步驟

```bash
php artisan vendor:publish --tag=passport-components
```

#### 5.建立頁面

在resources/assets/js/app.js，新增這幾行

```vue
Vue.component(
    'passport-clients',
    require('./components/passport/Clients.vue').default
);
 
Vue.component(
    'passport-authorized-clients',
    require('./components/passport/AuthorizedClients.vue').default
);
 
Vue.component(
    'passport-personal-access-tokens',
    require('./components/passport/PersonalAccessTokens.vue').default
);
```

##### 首頁

在resources/assets/view目錄，建立一個blade.php

```html
<div class="container">
        <div class="row">
            <div class="col-md-8 col-md-offset-2">
                <passport-clients></passport-clients>
                <passport-authorized-clients></passport-authorized-clients>
                <passport-personal-access-tokens></passport-personal-access-tokens>
            </div>
        </div>
    </div>
```

### 參考資料

- https://laravel.com/docs/6.x/passport
- https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E8%AA%8D%E8%AD%98-oauth-2-0-%E4%B8%80%E6%AC%A1%E4%BA%86%E8%A7%A3%E5%90%84%E8%A7%92%E8%89%B2-%E5%90%84%E9%A1%9E%E5%9E%8B%E6%B5%81%E7%A8%8B%E7%9A%84%E5%B7%AE%E7%95%B0-c42da83a6015
- https://docs.vmware.com/tw/VMware-Cloud-services/services/Using-VMware-Cloud-Services/GUID-53D39337-D93A-4B84-BD18-DDF43C21479A.html
- https://ithelp.ithome.com.tw/articles/10270271
