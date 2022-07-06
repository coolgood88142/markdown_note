# laravel passport跑第三方登入

## 大綱

- 什麼是laravel passport
- 循序圖
- 產生client、client_secret
- 新增第三方驗證
- 參考資料

### 什麼是laravel Passport

以前的身分驗證的方法是，透過傳統登入表單，在用API來驗證身分，並且要保持登入狀態，現在laravel 提供Laravel Passport讓API身份驗證變得更容易，Laravel Passport利用OAuth 2.0。

使用者在登入後，驗證完帳密後，系統會提供Access Token(授權碼)、Scope(所需要的資源清單)，在告知是否授權給系統，若同意的話，系統拿到Access Token後，會依照Scope提供的資訊，回傳使用者資料

使用者資料會有userID、姓名、大頭照等等，但是Scope則是會告知系統，我只會拿使用者的userID、姓名，並不會拿該使用者的全部資料

### 循序圖

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

### 產生client、client_secret

跑api建立client、client_secret

```php
public function oauthCilentData(){
    $client = Str::random(10);
    $client_secret = Str::random(40);
    
    $oauthCilent = new oauthCilent();
    $oauthCilent->client = $client;
    $oauthCilent->client_secret = $client_secret;
    $oauthCilent->save();
    
    return $oauthCilent;
}
```

### 新增第三方驗證

檢查使用者給的client_id是不是對的，回傳authorizationCode，一組編碼

```php
public function authorizationCode(ServerRequestInterface $psrRequest,
                              Request $request,
                              ClientRepository $clients,
                              TokenRepository $tokens)
    {
        return $this->withErrorHandling(function () use ($psrRequest, $request, $clients, $tokens) {
            $authRequest = $this->server->validateAuthorizationRequest($psrRequest);
            $scopes = $this->parseScopes($authRequest);

            $token = $tokens->findValidToken(
                $user = $request->user(),
                $client = $clients->find($authRequest->getClient()->getIdentifier())
            );

            if (($token && $token->scopes === collect($scopes)->pluck('id')->all()) ||
                $client->skipsAuthorization()) {
                return $this->approveRequest($authRequest, $user);
            }
        });
    }
```

成功之後，再跑一次api跑callback，檢查使用者給的authorizationCode、client_id、client_secret

```php
pubile function callback(Request $request){
     $http     = new GuzzleHttp\Client;
    $response = $http->post('http://127.0.0.1:8000/oauth/token', [
        'form_params' => [
            'grant_type'    => 'authorization_code',
            'redirect_uri'  => 'http://127.0.0.1:8080/callback',
            'code'          => $request->code,
            'client_id'     => $request->client_id,
            'client_secret' => $request->client_secret
        ]
    ]);

    return $queryString = json_decode((string)$response->getBody(), true);
}
```

跑完之後，這時會拿到AccessToken，再向laravel 要UserInfo的資料

```php
 public function getUserInfo(Request $request){
    $userInfo = $http->get('http://127.0.0.1:8000/api/user', [
        'headers' => [
            'Authorization' => 'Bearer '. $queryString['access_token'],
        ],
    ]);

    $user = json_decode((string)$userInfo->getBody(), true);
    return $user;
 }
```

拿到之後再跑一次，做登入

```php
public function verifyUserInfo(Request $request) {
        $user = User::where('email', '=', $request->email)->first();
        if($user == null) {
            //如果沒有client有帳號，要建立
            $user = User::create([
                'name' => $request->name,
                'email' => $request->email,
                'password' => $request->password,
            ]);
        }
        Auth::login($user);
        return redirect()->route('login');
    }
```

### 參考資料

- [OAuth2 authentication across Laravel projects - John Braun&#x27;s blog](https://johnbraun.blog/posts/oauth2-authentication-across-laravel-projects)
- [Node.js: social login in ExpressJS using Twitter, Google, Facebook and Linkedin with Passport | Gabriele Romanato](https://gabrieleromanato.name/nodejs-social-login-in-expressjs-using-twitter-google-facebook-and-linkedin-with-passport)
- https://developers.facebook.com/docs/facebook-login/android/?translation
- [Laravel Passport - Laravel - The PHP Framework For Web Artisans](https://laravel.com/docs/6.x/passport)
