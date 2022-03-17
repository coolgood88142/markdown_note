# laravel Passport

## 大綱

- 什麼是laravel Passport
- 什麼是OAuth
- OAuth循序圖
- laravel passport循序圖
- 為什麼要用這個code模式?
  - 要怎麼用code模式?
- 安裝步驟
  - 安裝套件
  - 設定config/app.php
  - 建立金鑰
  - 資料庫遷移
  - 建立可存取資料的Access Token
- 範例
- 參考資料

## 什麼是laravel Passport

以前的身分驗證的方法是，透過傳統登入表單，在用API來驗證身分，並且要保持登入狀態，現在laravel 提供Laravel Passport讓API身份驗證變得更容易，Laravel Passport利用OAuth 2.0。

使用者在登入後，驗證完帳密後，系統會提供Access Token(授權碼)、Scope(所需要的資源清單)，在告知是否授權給系統，若同意的話，系統拿到Access Token後，會依照Scope提供的資訊，回傳使用者資料

使用者資料會有userID、姓名、大頭照等等，但是Scope則是會告知系統，我只會拿使用者的userID、姓名，並不會拿該使用者的全部資料

## 什麼是OAuth

OAuth是一種授權的通訊協定，可向應用程式請求授權，取得資料的存取權，透過存取的Token獲得授權，並且用Token拿到資料

## OAuth循序圖

```sequence
Resource Owner->Client: 前往登入網頁
Note right of Client: 按下第三方登入按鈕
Client->Auth Server: Request Authorization Code URI
Note right of Auth Server: 檢查client_id\n導頁到登入畫面
Auth Server-->Resource Owner: Redirect login Page
Resource Owner->Auth Server: input account and password
Note right of Auth Server: 輸入帳密後做身份驗證
Auth Server->Auth Server: verification data
Note right of Auth Server: 驗證成功後顯示授權畫面
Auth Server-->Resource Owner: change authorize
Resource Owner->Auth Server: agree authorize
Note right of Auth Server: 授權同意後\n回傳Authorization Code
Auth Server-->Client: return Authorization Code
Client->Auth Server: get Access Token with Authorization Code
Note right of Auth Server: 拿Authorization Code\n回傳Access Token
Auth Server-->Client: return Access Token
Client->Resource Server: get User Info with Access Token
Note right of Resource Server: 拿Access Token\n回傳User Info
Resource Server-->Client: return User Info
Client->Client: verification user info
Note right of Client: 檢查User Info是否有註冊\n沒有就建立資料做登入後\n有的話就直接登入
Client->Resource Owner: sign in success
```

1. Resourse Owner(user) 按下第三方登入按鈕
2. Client 請求Authorization Code 檢查client_id 導頁到登入畫面
3. Resourse Owner(user) 輸入帳密後做身份驗證
4. Auth Server 驗證成功後顯示授權畫面
5. Resourse Owner(user) 授權同意後回傳Authorization Code
6. Client 拿Authorization Code 回傳Access Token
7. Client 拿Access Token 回傳User Info
8. Client  檢查User Info是否有註冊，沒有就建立資料做登入後，有的話就直接登入
9. Resourse Owner(user) 看到登入成功畫面

## laravel passport循序圖

```sequence
Resource Owner->Client: 前往登入網頁
Note right of Client: 按下第三方登入按鈕
Client->Auth Server: 檢查client_id
Note right of Auth Server: 進入authorize route
Auth Server-->Resource Owner: 導頁到passport的登入畫面
Resource Owner->Auth Server: 輸入帳號與密碼
Auth Server->Auth Server: 帳密資料做身份驗證
Note right of Auth Server: 驗證成功後\n顯示授權畫面
Auth Server-->Resource Owner: user 受是否同意授權
Resource Owner->Auth Server: 同意授權
Note right of Auth Server: 授權同意後\n回傳Authorization Code
Auth Server-->Client: 傳 Authization Code
Client->Auth Server: 用Authization Code拿Access Token
Note right of Auth Server: 拿Authization Code\n回傳Access Token
Auth Server-->Client: 傳 Access Token
Client->Resource Server: 用Access Token拿User Info
Note right of Resource Server: 拿Access Token\n回傳User Info
Resource Server-->Client: 傳User Info
Client->Client: 驗證user info
Note right of Client: 檢查User Info是否有註冊\n沒有就建立資料做登入後\n有的話就直接登入
Client->Resource Owner: 登入成功
```

1. Resourse Owner(user) 按下第三方登入按鈕

2. Client 請求Authorization Code 檢查client_id 進到authorize route

3. Resourse Owner(user) 輸入帳密後做身份驗證

4. laravel passport 驗證帳密成功後顯示授權畫面

5. Resourse Owner(user) 授權同意後回傳Authorization Code

6. Client 拿Authorization Code 回傳Access Token

7. Client 拿Access Token 回傳User Info

8. Client 檢查User Info是否有註冊，沒有就建立資料做登入後，有的話就直接登入

9. Resourse Owner(user) 看到登入成功畫面

## 為什麼要用這個code模式?

因為我們同意授權的時候，client不會向user拿帳密，只要登入第三方平台的資料，並且詢問user 同意是否授權資料給client，就可以利用第三方平台的資料做登入

### 要怎麼用code模式?

當user同意授權時，client會發送給clientID、scopes、redirect URI，因此第三方平台，會知道要拿什麼資料回來，傳到哪裡

## 安裝步驟

### 安裝套件

```bash
composer require laravel/passport 7.5.1
```

### 設定config/app.php

providers中新增這行

```bash
Laravel\Passport\PassportServiceProvider::class
```

### 建立金鑰

執行下面這行指令建立金鑰，成功後專案storage資料夾，會產生oauth-private.key、oauth-public.key

```bash
 php artisan passport:keys 
```

### 資料庫遷移

```bash
php artisan migrate
```

### 建立可存取資料的Access Token

```bash
php artisan passport:install
```

## 範例

用PassPort做第三方登入，在Client平台做登入，成功後要顯示登入後畫面，要建立兩個專案laravel_client(client)、laravel_passport(passport)，以下範例的版本為laravel 6

#### 切換passport專案

```bash
cd laravel_passport
```

### 1.User Model新增檢查身份驗證機制

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

### 2.AuthServiceProvider 加入Passport

新增Passport::routes()，這裡會將authorize route跑到laravel passport套件的Controller

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

### 3.修改config/auth.php

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

### 4.發布 Passport Vue

passport有內建的vue component，要用之前，請先安裝好Vue之後，在做這個步驟

```bash
--安裝vue 2.6.12
npm install --save-dev vue@2.6.12
```

```bash
php artisan vendor:publish --tag=passport-components
```

### 5.建立頁面

在resources/assets/js/app.js，新增下方程式碼，passport-client component、passport-authorized-clients、passport-personal-access-tokens，3種component

```javascript
//建立client資料介面
//這裡要注意，新增資料會寫入到DB上的oauth_client，是記錄當前的user建立client資料，並不會影響登入驗證
//如果不需要可以用php artisan passport:client指令，新增client資料
Vue.component(
    'passport-clients',
    require('./components/passport/Clients.vue').default
);

//顯示用passport第三方登入，有哪些client資料
Vue.component(
    'passport-authorized-clients',
    require('./components/passport/AuthorizedClients.vue').default
);

//建立access token，這裡只有測試資料才會用到
Vue.component(
    'passport-personal-access-tokens',
    require('./components/passport/PersonalAccessTokens.vue').default
```

#### 首頁

在resources/assets/view目錄，建立一個blade.php，將3種component放到頁面上

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

### 6.安裝laravel ui

這裡將passport、client，兩個專案都安裝

```bash
composer require laravel/ui
```

### 7.編譯js

```bash
npm run dev
```

編譯好之後啟動專案看看，瀏覽器輸入http://127.0.0.1:8000

```bash
php artisan serve
```

啟動專案會顯示下面該畫面，在weclome.blade.php可自行修改標題文<img title="" src="file:///C:/xampp/htdocs/markdown_note/assets/images/passport1.png" alt="tet" width="652">選REGISTER註冊一個帳號，用來做登入

![tet](C:\xampp\htdocs\markdown_note\assets\images\passport2.png)

#### 切換client專案

```bash
cd laravel_client
```

### 8.安裝guzzlehttp/guzzle套件

```bash
composer require guzzlehttp/guzzle 
```

### 9.設定路由

在route/web.php，新增下方程式碼

```php
//建立authorize Route，這裡是client向laravel passport請求Authorization Code 
Route::get('/authorize', function (Request $request) {
    //建立一個隨機40個字母的字串資料，並且設定到session裡
    //用途是如果有多個使用者，都是用相同client做登入，要用這組編碼做識別
    //第三方登入(FB)才會知道回傳資料給誰
    $request->session()->put('state', $state = Str::random(40));

    //建立authoriz參數，http_build_query是PHP的內建函式，會產生一個URL的encode 
    $query = http_build_query([
         //client_id將之前步驟，建立成功client_id的放到這裡，或者可以用在route傳參數進來
        'client_id' => $request->clientId,    //clientID

         //redirect_uri是拿到Authorization Code後，並且同意授權後的網址
         //這裡的8080port為client專案，設定callback route
        'redirect_uri' => 'http://127.0.0.1:8080/callback',

         //設定type為code
        'response_type' => 'code',

        //設定請求資料範圍，沒有填就是拿全部，可針對user table欄位拿資料
        'scope' => '',    

        //state參數主要是用來確認client的身份唯一碼
        'state' => $state,
    ]);

    //當client取得Authorization Code，去跑passport的oauth/authorize
    return redirect('http://127.0.0.1:8000/oauth/authorize?'.$query);
});
```

#### oauth/authorize Route

從套件中的Passport.php的routes()，已經設定route前面要加上oauth

```php
//laravel_passport\vendor\laravel\passport\src\Passport.php
public static function routes($callback = null, array $options = [])
    {
        //在跑進來之後會先判斷是否帶參數，沒有就跑all()
        $callback = $callback ?: function ($router) {
            $router->all();
        };

        //設定每個route前面要加oauth，來自於Laravel\Passport\Http\Controllers
        $defaultOptions = [
            'prefix' => 'oauth',
            'namespace' => '\Laravel\Passport\Http\Controllers',
        ];

        //設定options變數與$defaultOptions變數的array，做整併
        $options = array_merge($defaultOptions, $options);

        //設定所有route都要先跑RouteRegistrar
        //laravel_passport\vendor\laravel\passport\src\RouteRegistrar.php
        Route::group($options, function ($router) use ($callback) {
            $callback(new RouteRegistrar($router));
        });
    }
```

套件中的RouteRegistrar.php的all()，會跑檔案中forAuthorization()、forAccessTokens()、forTransientTokens()、forClients()、forPersonalAccessTokens()

```php
//laravel_passport\vendor\laravel\passport\src\RouteRegistrar@all()
public function all()    
    {
        $this->forAuthorization();
        $this->forAccessTokens();
        $this->forTransientTokens();
        $this->forClients();
        $this->forPersonalAccessTokens();
    }
```

從套件中的RouteRegistrar.php，發現authorize route會去跑RouteRegistrar的forAuthorization()

```php
//laravel_passport\vendor\laravel\passport\src\RouteRegistrar@forAuthorization()
public function forAuthorization()
    {
        //設定每個route都會引用middleware的web、auth兩個class
        //middleware是來自於laravel_passport\app\Http\Kernel.php@routeMiddleware
        $this->router->group(['middleware' => ['web', 'auth']], function ($router) {

            //使用get /authorize route，會去跑AuthorizationController的authorize()
            //route name為passport.authorizations.authorize
            $router->get('/authorize', [
                'uses' => 'AuthorizationController@authorize',
                'as' => 'passport.authorizations.authorize',
            ]);

            //這裡是授權畫面的同意route
            //使用post /authorize route，會去跑ApproveAuthorizationController的approve()
            //route name為passport.authorizations.approve
            $router->post('/authorize', [
                'uses' => 'ApproveAuthorizationController@approve',
                'as' => 'passport.authorizations.approve',
            ]);

            //這裡是授權畫面的不同意route
            //使用delete /authorize route，會去跑DenyAuthorizationController的deny()
            //route name為passport.authorizations.deny
            $router->delete('/authorize', [
                'uses' => 'DenyAuthorizationController@deny',
                'as' => 'passport.authorizations.deny',
            ]);
        });
    }
```

有先跑middleware的auth，會先到首頁輸入帳密後

```html

```

，繼續從AuthorizationController的authorize()，請求Authorization Code

```php
//laravel_passport\vendor\laravel\passport\src\Http\Controllers\AuthorizationController@authorize()
public function authorize(ServerRequestInterface $psrRequest,
                              Request $request,
                              ClientReposiory $cients,
                              TokenRepository $tokens)
    {
        //先跑HandlesOAuthErrors的withErrorHandling
        return $this->withErrorHandling(function () use ($psrRequest, $request, $clients, $tokens) {
            //AuthorizationServer去檢查請求的資料
            $authRequest = $this->server->validateAuthorizationRequest($psrRequest);

            //執行parseScopes()，找請求的資料的參數scope，賦予$scopes變數成為集合
            $scopes = $this->parseScopes($authRequest);

            //驗證token資料
            $token = $tokens->findValidToken(
                $user = $request->user(),
                $client = $clients->find($authRequest->getClient()->getIdentifier())
            );

            //用token的scopes 資料跟scope變數比對資料是否相同
            //相同的話authRequest變數，會做更新user物件
            if (($token && $token->scopes === collect($scopes)->pluck('id')->all()) ||
                $client->skipsAuthorization()) {
                return $this->approveRequest($authRequest, $user);
            }

            //更新session的authRequest物件
            $request->session()->put('authRequest', $authRequest);

            //回傳授權畫面，並且帶client、user、scopes、request
            return $this->response->view('passport::authorize', [
                'client' => $client,
                'user' => $user,
                'scopes' => $scopes,
                'request' => $request,
            ]);
        });
    }
```

顯示authorize.blade.php(授權畫面)

點選同意時，會跑route passport.authorizations.approve，如果是不同意，跑passport.authorizations.deny，都會帶state、client_id參數

```html
<!--laravel_passport\resources\views\vendor\passport\authorize.blade.php-->
<body class="passport-authorize">
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-6">
                <div class="card card-default">
                    <div class="card-header">
                        Authorization Request
                    </div>
                    <div class="card-body">
                        <!-- Introduction -->
                        <!--客戶端名稱-->
                        <p><strong>{{ $client->name }}</strong> is requesting permission to access your account.</p>

                        <!-- Scope List -->
                        @if (count($scopes) > 0)
                            <div class="scopes">
                                    <p><strong>This application will be able to:</strong></p>

                                    <ul>
                                        <!--顯示請求資料時，有設定那些範圍資料-->
                                        @foreach ($scopes as $scope)
                                            <li>{{ $scope->description }}</li>
                                        @endforeach
                                    </ul>
                            </div>
                        @endif

                        <div class="buttons">
                            <!--授權同意按鈕-->
                            <!-- Authorize Button -->
                            <form method="post" action="{{ route('passport.authorizations.approve') }}">
                                {{ csrf_field() }}

                                <input type="hidden" name="state" value="{{ $request->state }}">
                                <input type="hidden" name="client_id" value="{{ $client->id }}">
                                <button type="submit" class="btn btn-success btn-approve">Authorize</button>
                            </form>

                            <!--授權不同意按鈕-->
                            <!-- Cancel Button -->
                            <form method="post" action="{{ route('passport.authorizations.deny') }}">
                                {{ csrf_field() }}
                                {{ method_field('DELETE') }}

                                <input type="hidden" name="state" value="{{ $request->state }}">
                                <input type="hidden" name="client_id" value="{{ $client->id }}">
                                <button class="btn btn-danger">Cancel</button>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
```

同意授權跑ApproveAuthorizationController的approve()

```php
//laravel_passport\vendor\laravel\passport\src\Http\Controllers\ApproveAuthorizationController@approve()
public function approve(Request $request)
    {
        return $this->withErrorHandling(function () use ($request) {
            //將授權畫面上的state、client_id資料，設定到session，並且賦予變數authRequest 
            $authRequest = $this->getAuthRequestFromSession($request);

            //回傳authRequest變數資料
            return $this->convertResponse(
                $this->server->completeAuthorizationRequest($authRequest, new Psr7Response)
            );
        });
    }
```

這時前面route的middleware，有用到auth，會去跑Authenticate.php的redirectTo，但是回傳的資料不是json格式，會導頁到login

```php
//laravel_passport\App\Http\Middleware\Authenticate@redirectTo
protected function redirectTo($request)
    {
        //request變數如果不是json格式就回到首頁
        if (! $request->expectsJson()) {
            return route('login');
        }
    }
```

不同意授權跑DenyAuthorizationController的deny()

```php
public function deny(Request $request)
    {
        //將授權畫面上的state、client_id資料，設定到session，並且賦予變數authRequest 
        $authRequest = $this->getAuthRequestFromSession($request);

        //判斷是否有client的redirectUri資料，並且將redirectUri資料賦予clientUris變數
        $clientUris = Arr::wrap($authRequest->getClient()->getRedirectUri());

        //判斷authRequest的redirectUri跟clientUris變數資料是否相同
        //如果沒有就將authRequest的redirectUri資料，變成clientUris的第一筆
        if (! in_array($uri = $authRequest->getRedirectUri(), $clientUris)) {
            $uri = Arr::first($clientUris);
        }

        //判斷authRequest的grantTypeId是否為implicit，如果是separator為#符號
        //不是就會判斷uri變數第一個字是否為?，是的話就回傳&符號，不是的話回傳?符號
        $separator = $authRequest->getGrantTypeId() === 'implicit' ? '#' : (strstr($uri, '?') ? '&' : '?');

        //會跑redirectUri，並且帶error跟state參數
        return $this->response->redirectTo(
            $uri.$separator.'error=access_denied&state='.$request->input('state')
        );
    }
```

```php
Route::get('/callback', function (Request $request) {
    $http     = new GuzzleHttp\Client;
    $response = $http->post('http://127.0.0.1:8000/oauth/token', [
        'form_params' => [
            'grant_type'    => 'authorization_code',
            'redirect_uri'  => 'http://127.0.0.1:8080/callback',
            'code'          => $request->code,
            'client_id'     => '19',
            'client_secret' => 'YwvWN4dVe0blF7zpwAl2ge61ksTYBrvY8J8NfMT6'
        ]
    ]);

    $queryString = json_decode((string)$response->getBody(), true);

    $query = http_build_query([
        'Authorization' => 'Bearer '. $queryString['access_token'],
    ]);

    return redirect('http://127.0.0.1:8000/api/user/profile');
});
```

程式碼流程

1. **/authorize**  Kai Blog依照參數client_id、redirect_uri、response_type、scope、state，導頁到FB(passport)的login

2. **/oauth/authorize** FB(passport)會從套件上的**vendor/laravel/passport/src/Http/Controllers/AuthorzationController**的**authorize()** 會先驗證帳密資料，才決定是否要顯示授權畫面

3. **/callback** 從FB(passport)取得

### 參考資料

- https://laravel.com/docs/6.x/passport
- https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E8%AA%8D%E8%AD%98-oauth-2-0-%E4%B8%80%E6%AC%A1%E4%BA%86%E8%A7%A3%E5%90%84%E8%A7%92%E8%89%B2-%E5%90%84%E9%A1%9E%E5%9E%8B%E6%B5%81%E7%A8%8B%E7%9A%84%E5%B7%AE%E7%95%B0-c42da83a6015
- https://docs.vmware.com/tw/VMware-Cloud-services/services/Using-VMware-Cloud-Services/GUID-53D39337-D93A-4B84-BD18-DDF43C21479A.html
- https://ithelp.ithome.com.tw/articles/10270271
