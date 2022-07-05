# laravel Socialite

## 大綱

- 什麼是laravel Socialite
- 安裝步驟
  - 安裝套件
  - 設定config/app.php
  - 設定config/services.php
- 建立Contorller
- 設定route
- 註冊GitHub應用程式
- 測試跑GitHub
- 建立laravel Ui
- 建立User Table
- 新增GitHub第三方登入按鈕
- 建立第三方登入程式碼
- 參考資料

## 什麼是laravel Socialite

Laravel提供Laravel Socialite可通過OAuth提供程序進行身份驗證的簡便方法，目前支持Facebook，Twitter，LinkedIn，Google，GitHub，GitLab和Bitbucket進行身份驗證。

官網提供[Socialite Providers](https://socialiteproviders.netlify.com/about.html)，網站中有各式可供第三方服務的登入認證，以Github為範例

![laravelSocialite1.png](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/laravelSocialite1.png)

## 安裝步驟

### 安裝套件

```bash
composer require laravel/socialite
composer require socialiteproviders/github
```

### 設定config/app.php

移除SocialiteServiceProvider，新增ServiceProvider

```bash
'providers' => [
    // a whole bunch of providers
    // remove 'Laravel\Socialite\SocialiteServiceProvider',
    \SocialiteProviders\Manager\ServiceProvider::class, // 新增
];
```

### 設定config/services.php

新增下面這行

```php
'github' => [
    'client_id' => env('GITHUB_CLIENT_ID'),
    'client_secret' => env('GITHUB_CLIENT_SECRET'),
    'redirect' => 'http://your-callback-url',
],
```

### 建立Contorller

```bash
php artisan make:controller SocialController
```

建立之後打開SocialController，新增use Socialite

```php
namespace App\Http\Controllers\Auth;

use Socialite;

class SocialController extends Controller
{
    /**
     * 將用戶重定向到GitHub身份驗證頁面。
     *
     * @return \Illuminate\Http\Response
     */
    public function redirectToProvider()
    {
        return Socialite::driver('github')->redirect();
    }

    /**
     * 從GitHub獲取用戶信息。
     *
     * @return \Illuminate\Http\Response
     */
    public function handleProviderCallback()
    {
        $user = Socialite::driver('github')->user();

        // $user->token;
    }
}
```

### 設定route

```php
//將用戶重新導向至OAuth提供程序
Route::get('login/github', 'Auth\LoginController@redirectToProvider');

//在身份驗證之後接收來自提供程序的回調。
Route::get('login/github/callback', 'Auth\LoginController@handleProviderCallback');
```

### 註冊GitHub應用程式

1. 進入GitHub選擇setting，在選到Developer settings
   
   ![laravelSocialite2.png](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/laravelSocialite2.png)

2. 建立OAuth application
   
   ![laravelSocialite3.png](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/laravelSocialite3.png)

3. 成功之後，將ClientID、Client secrets，新增到config/services.php
   
   ![laravelSocialite4.png](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/laravelSocialite4.png)
   
   ```php
   'github' => [
           'client_id' =>'**********',
           'client_secret' => '******************************',
           'redirect' => 'http://127.0.0.1:8000/login/github/callback',
       ],
   ```

### 測試跑GitHub

執行php artisan serve，在網址跑http://127.0.0.1:8000/login/github/callback，會顯示下面的畫面

<img title="" src="https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/laravelSocialite5.png" alt="laravelSocialite5.png" width="646">

### 建立laravel Ui

安裝laravel/ui套件

```bash
composer require laravel/ui --dev

php artisan ui vue --auth
```

![laravelSocialite6.png](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/laravelSocialite6.png)

### 建立User Table

在database/migrations/xxx_create_users_table.php，laravel本來就會建立好table 欄位

```bash
php artisan migrate 
```

### 新增GitHub第三方登入按鈕

在login.blade.php，新增下面的html

```php
 <a href="login/github" class="btn btn-primary">
     <span style="margin-right: 10px">
        <svg width="24px" height="24px" viewBox="0 0 24 24" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
            <g id="Page-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                 <g transform="translate(-614.000000, -553.000000)" fill="#FFFFFF" fill-rule="nonzero">
                    <g id="github" transform="translate(614.000000, 553.000000)">
                       <path d="M12,0.297 C5.37,0.297 0,5.67 0,12.297 C0,17.6 3.438,22.097 8.205,23.682 C8.805,23.795 9.025,23.424 9.025,23.105 C9.025,22.82 9.015,22.065 9.01,21.065 C5.672,21.789 4.968,19.455 4.968,19.455 C4.422,18.07 3.633,17.7 3.633,17.7 C2.546,16.956 3.717,16.971 3.717,16.971 C4.922,17.055 5.555,18.207 5.555,18.207 C6.625,20.042 8.364,19.512 9.05,19.205 C9.158,18.429 9.467,17.9 9.81,17.6 C7.145,17.3 4.344,16.268 4.344,11.67 C4.344,10.36 4.809,9.29 5.579,8.45 C5.444,8.147 5.039,6.927 5.684,5.274 C5.684,5.274 6.689,4.952 8.984,6.504 C9.944,6.237 10.964,6.105 11.984,6.099 C13.004,6.105 14.024,6.237 14.984,6.504 C17.264,4.952 18.269,5.274 18.269,5.274 C18.914,6.927 18.509,8.147 18.389,8.45 C19.154,9.29 19.619,10.36 19.619,11.67 C19.619,16.28 16.814,17.295 14.144,17.59 C14.564,17.95 14.954,18.686 14.954,19.81 C14.954,21.416 14.939,22.706 14.939,23.096 C14.939,23.411 15.149,23.786 15.764,23.666 C20.565,22.092 24,17.592 24,12.297 C24,5.67 18.627,0.297 12,0.297" id="Shape"></path>
                    </g>
                 </g>
             </g>
        </svg>
      </span>
     Login with GitHub
 </a>
```

![laravelSocialite7.png](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/laravelSocialite7.png)

### 建立第三方登入程式碼

在SocialController，新增下面的程式碼

```php
public function handleProviderCallback()
    {    
        //建立github第3方登入物件，取得使用者資料
        $user = Socialite::driver('github')->user();

        //比對資料表與使用者資料
        $account = User::where('email',$user->email)->first();

        //如果沒資料，就會直接建立新增使用者資料
        if(!$account){
            User::create([
                'email'=>$user->email,
                'name'=>$user->nickname,
                'password'=>bcrypt('githubtest')
            ]);
        }

         // 登入並且「記住」使用者
        Auth::login($account);
        //回到首頁
        return redirect('/');
```

輸入http://127.0.0.1:8000/login ，點選<img title="" src="https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/laravelSocialite8.png" alt="laravelSocialite8.png" width="160"> 系統會導頁到GitHub登入頁面 

### 參考資料

- https://ithelp.ithome.com.tw/articles/10227884
- https://socialiteproviders.com/GitHub/#installation-basic-usage
- https://techvblogs.com/blog/social-login-laravel-socialite
- https://developer.apple.com/forums/thread/56077
- https://www.honeybadger.io/blog/laravel-oauth/
