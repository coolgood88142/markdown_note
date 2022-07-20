# laravel passport跑手機的第三方登入

## 大綱

- 什麼是laravel passport
- 介紹
- 循序圖
- 產生client、client_secret
- 新增第三方驗證
- 參考資料

### 什麼是laravel Passport

以前的身分驗證的方法是，透過傳統登入表單，在用API來驗證身分，並且要保持登入狀態，現在laravel 提供Laravel Passport讓API身份驗證變得更容易，Laravel Passport利用OAuth 2.0。

使用者在登入後，驗證完帳密後，系統會提供Access Token(授權碼)、Scope(所需要的資源清單)，在告知是否授權給系統，若同意的話，系統拿到Access Token後，會依照Scope提供的資訊，回傳使用者資料

使用者資料會有userID、姓名、大頭照等等，但是Scope則是會告知系統，我只會拿使用者的userID、姓名，並不會拿該使用者的全部資料

### 介紹

以下介紹使用手機在Kai Blog做第三方登入(FB)，使用者登入後系統會確認，該帳號有沒有client，有的話就繼續，沒有就建立

### 循序圖

```sequence
Moblie-->Auth Server: 請求Authorization Code
Note right of Auth Server: 進入authorize route
Auth Server->Moblie: 回傳Authorization Code
Moblie-->Client: 傳Authorization Code
Client->Auth Server: 用Authization Code拿Access Token
Note right of Auth Server: 拿Authization Code\n回傳Access Token
Auth Server-->Client: 傳 Access Token
Client->Resource Server: 用Access Token拿User Info
Note right of Resource Server: 拿Access Token\n回傳User Info
Resource Server-->Client: 傳User Info
Client->Client: 驗證user info
Note right of Client: 檢查User Info是否有註冊\n沒有就建立資料做登入後\n有的話就直接登入
Client->Moblie: 登入成功
```

1. Mobile(user) 按下第三方登入按鈕

2. Mobile 請求Authorization Code 檢查client_id 進到authorize route

3. Mobile(user) 拿到Authorization Code

4. Mobile(user) 拿到Authorization Code給Client

5. Client 拿Authorization Code 回傳Access Token

6. Client 拿Access Token 回傳User Info

7. Client 檢查User Info是否有註冊，沒有就建立資料做登入後，有的話就直接登入

8. Mobile(user) 看到登入成功畫面

### 跑authorize route

跑進來之前會先到laravel Middleware，跑登入畫面，登入之後才會到authorize，會先確認登入的使用者有沒有Cilent ID，沒有就建一個，這時就會去controller 的authorizationCode拿Authorization Code

```php
 Route::get('/authorize', function (Request $request) {
    $userId = Auth::user()->id;
    $oauthAuthCodes = OauthAuthCodes::where('user_id', '=', $userId)->first();
    $oauthCilent = '';
    $clientId = '';
    $redirectUri= '';
    $state = Str::random(40);

    if($oauthAuthCodes->count() == 0){
      $oauthCilents = this->oauthCilentData();
      $clientId = $oauthCilents->client_id;
      $redirectUri= $oauthCilents->redirect_uri;
    }else{
      $clientId = $oauthAuthCodes->client_id;
      $redirectUri= $oauthAuthCodes->redirect_uri;
    }

    $query = http_build_query([
        'client_id' => $clientId,
        'redirect_uri' => $redirectUri,
        'response_type' => 'code',
        'scope' => '',
        'state' => $state,
    ]);

    return redirect('http://127.0.0.1:8000/authorizationCode?'.$query);
});
```

### login登入

使用者用手機點第三方登入會跑到登入畫面，輸入完之後先驗證帳密

```php
public function login(Request $request)
    {
        $request->validate([
            'email' => 'required|string|email',
            'password' => 'required|string',
            'remember_me' => 'boolean'
        ]);

        $credentials = request(['email', 'password']);

        if(!Auth::attempt($credentials))
            return response()->json([
                'message' => 'Unauthorized'
            ], 401);

        $user = $request->user();

        $tokenResult = $user->createToken('Personal Access Token');
        $token = $tokenResult->token;

        $token->expires_at = Carbon::now()->addWeeks(1);
        $token->save();

        return response()->json([
            'access_token' => $tokenResult->accessToken,
            'token_type' => 'Bearer',
            'expires_at' => Carbon::parse(
                $tokenResult->token->expires_at
            )->toDateTimeString()
        ]);
    }
```

### 產生client、client_secret

跑api建立client、client_secret

```php
public function oauthCilentData(){
    $client = Str::random(10);

    $oauthCilent = new oauthCilent();
    $oauthCilent->client = $client;
    $oauthCilent->redirect = 'http://127.0.0.1:8080/callback';
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
public function callback(Request $request){
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
        //進入登入畫面
        Auth::login($user);
        return redirect()->route('login');
    }
```

### 參考資料

- [OAuth2 authentication across Laravel projects - John Braun&#x27;s blog](https://johnbraun.blog/posts/oauth2-authentication-across-laravel-projects)
- [Node.js: social login in ExpressJS using Twitter, Google, Facebook and Linkedin with Passport | Gabriele Romanato](https://gabrieleromanato.name/nodejs-social-login-in-expressjs-using-twitter-google-facebook-and-linkedin-with-passport)
- https://developers.facebook.com/docs/facebook-login/android/?translation
- [Laravel Passport - Laravel - The PHP Framework For Web Artisans](https://laravel.com/docs/6.x/passport)
- [security - Authorization Code Flow, sending the code from a mobile app to a REST API - Stack Overflow](https://stackoverflow.com/questions/62703856/authorization-code-flow-sending-the-code-from-a-mobile-app-to-a-rest-api)
- [What is mobile authentication? - Definition from WhatIs.com](https://www.techtarget.com/searchsecurity/definition/mobile-authentication)
- [Request a mobile authorization code without server · Issue #4 · square/reader-sdk-ios-quickstart · GitHub](https://github.com/square/reader-sdk-ios-quickstart/issues/4)
