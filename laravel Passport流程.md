目標：在Kai Blog按鈕FB登入，並且用FB資料做登入

出發點：Route (web.php)

1. route (laravel_client-web.php)
   
   1. 檔案路徑：route\web.php
      
      function:Route::get('/', function () {});
      
      用途：回傳指定的view，welcome.blade.php
   
   2. 檔案路徑：resources\views\welcome.blade.php
      
      function：addEventListener('click', function{})
      
      用途：按下按鈕前往authorize route
   
   3. 檔案路徑：route\web.php
      
      function: Route::get('/authorize', function (Request $request) {})
      
      用途：用redirect導頁到http://127.0.0.1:8080/authorizationCode，帶query參數
      
      做了什麼事：設定state、client_id、redirect_uri、response_type、scope參數，在把這些放到http_build_query()，並且用query參數，在用到redirect導頁到http://127.0.0.1:8080/authorizationCode
      
          state：建立一個隨機40個字母的字串資料，並且設定到session裡
      
          client_id：設定client_id
      
          redirect_uri：設定同意授權後的網址
      
          response_type：設定type為code
      
          scope：設定請求資料範圍

2. route (larvel_passport-web.php)
   
   檔案路徑：route\web.php
   
   function：Route::group(['middleware' => ['web', 'oauth']], function ($router) {Route::get('/authorizationCode', [])})
   
   用途：在進入authorizationCode route之前，會先進入middleware的web、oauth
   
   做了什麼事：進入middleware找web、oauth對應的class是什麼?

3. HttpKernel (Kernel.php)
   
   1. 檔案路徑：app\Http\Kernel.php
      
      function：syncMiddlewareToRouter()
      
      用途：去找routeMiddleware的key oauth，value是什麼?
      
      做了什麼事：在Kernel的routeMiddleware陣列中，有個key叫oauth，指向的class是OauthAuthenticate

4. middleware (OauthAuthenticate.php)
   
   1. 檔案路徑：app\Http\Middleware\OauthAuthenticate.php
      
      function：redirectTo()
      
      用途：設定當尚未登入時，要轉址到的route -> 在Authenticate.php中的unauthenticated()使用到
      
      做了什麼事：設定完unauthenticated，就判斷request變數是否為json，如果不是就跑route login
   
   2. 檔案路徑：vendor/laravel/framework/src/Illuminate/Auth/Middleware/Authenticate.php
      function：unauthenticated()
      用途：處理尚未登入的使用者，該前往哪裡
      做了什麼事：丟了一個exception，AuthenticationException()
   
   3. 檔案路徑：vendor/laravel/framework/src/Illuminate/Auth/AuthenticationException.php
      
      function：__construct()
      
      用途：建立exception，程式拋錯時做什麼事
      
      做了什麼事：當程式拋錯的時候，要前往哪個url
   
   4. 檔案路徑：
      
      vendor\laravel\framework\src\Illuminate\Foundation\Exceptions\Handler.php
      
      function：unauthenticated()
      
      用途：跑AuthenticationException的時候，先判斷request的格式，是否為json
      
      做了什麼事：如果是的話，顯示401錯誤訊息，用json格式回傳，如果不是的話，將rediect物件設定response
   
   5. 檔案路徑：
      
      vendor\laravel\framework\src\Illuminate\Routing\Redirector.php
      
      function：guest()
      
      用途：將rediect物件設定response，並且設定到session上
      
      做了什麼事：判斷Request是否用GET、是否有route、格式是否為json，如果有的話，回傳完整的URL，如果不是，回傳request的header中referer資料
   
   6. 檔案路徑：
      
      vendor\laravel\framework\src\Illuminate\Routing\Redirector.php
      
      function：to()
      
      用途：重新定義rediect的url
      
      做了什麼事：建立新的rediect物件
   
   7. 檔案路徑：
      
      vendor\laravel\framework\src\Illuminate\Routing\Redirector.php
      
      function：createRedirect()
      
      用途：建立新的rediect物件
      
      做了什麼事：建立新的rediect物件，並且設定到response
   
   8. 檔案路徑：
      
      vendor\laravel\framework\src\Illuminate\Support\helpers.php
      
      function：tap()
      
      用途：跑共用的function，並且用callback
      
      做了什麼事：建立新的rediect物件，並且設定到response，判斷rediect物件的session，有資料就設定session裡，在將rediect物件設定request物件
   
   9. 檔案路徑：
      
      app\Http\Middleware\Authenticate.php
      
      function：! $request->expectsJson()
      
      用途：判斷request變數是否為json
      
      做了什麼事：如果不是就跑route login
   
   10. 檔案路徑：
       
       resources\views\auth\login.blade.php
       
       function：submit()
       
       用途：輸入完帳密後，點選登入按鈕
       
       做了什麼事：將帳密資料做驗證

5. middleware (LoginController.php)

6. 

在Kai Blog按下FB登入

passport.addEventListener("click", function() {})

laravel_client\resources\views\welcome.blade.php 

1.1 window.location.href = http://127.0.0.1:8080/authorize 網頁導頁到http://127.0.0.1:8080/authorize

1. localhost:8080/authorize 跑passport的oauth\authorize
   
   Route::get('/authorize', function (Request $request) {})
   
   laravel_client\routes\web.php
   
   2.1 state = session->put('state', $state = Str::random(40)) state變數，建立一個隨機40個字母的字串資料，並且設定到session裡
   
   2.2 http_build_query，http_build_query是PHP的內建函式，會產生一個URL的encode
   
   2.3 'client_id' => $request->clientId, 設定client_id
   
   2.4 'redirect_uri' => 'http://127.0.0.1:8080/callback', 設定同意授權後的網址
   
   2.5 'response_type' => 'code', 設定type為code
   
   2.6 'scope' => '', 設定請求資料範圍
   
   2.7 'state' => $state, 設定state參數主要是用來確認client的身份唯一碼
   
   2.8 redirect('http://127.0.0.1:8000/oauth/authorize?'.$query) 當client取得Authorization Code，去跑passport的oauth/authorize

2. localhost:8000/oauth/authorize 跑第三方登入
   
   public static function routes(callback = null, array options = [])
   
   laravel_passport\\vendor\laravel\passport\src\Passport.php
   
   3.1 callback = callback ?: function (router) {
    router->all();
    };
   
   在跑進來之後會先判斷是否帶參數，沒有就跑all()
   
   3.2 defaultOptions = [
    'prefix' => 'oauth',
    'namespace' => '\Laravel\Passport\Http\Controllers',
    ];
   
   設定每個route前面要加oauth，來自於Laravel\Passport\Http\Controllers
   
   3.3 options = array_merge(defaultOptions, options);
   
   設定options變數與$defaultOptions變數的array，做整併
   
   3.4 Route::group(options, function (router) use (callback) {
    callback(new RouteRegistrar($router));
    });
   
   設定所有route都要先跑RouteRegistrar
   
   3.4.1 new RouteRegistrar($router)
   
   laravel_passport\vendor\laravel\passport\src\RouteRegistrar.php
   
   3.4.2 this->router->group(['middleware' => ['web', 'auth']], function (router) {} 設定每個route都會引用middleware的web、auth兩個class
   
   3.4.3 $router->get('/authorize', [
    'uses' => 'AuthorizationController@authorize',
    'as' => 'passport.authorizations.authorize',
    ]);
   
   使用get /authorize route，會去跑AuthorizationController的authorize()
    route name為passport.authorizations.authorize
   
   3.4.4 $router->post('/authorize', [
    'uses' => 'ApproveAuthorizationController@approve',
    'as' => 'passport.authorizations.approve',
    ]);
   
   使用post /authorize route，會去跑ApproveAuthorizationController的approve()
    route name為passport.authorizations.approve
   
   3.4.5 $router->delete('/authorize', [
    'uses' => 'DenyAuthorizationController@deny',
    'as' => 'passport.authorizations.deny',
    ]);
   
   使用delete /authorize route，會去跑DenyAuthorizationController的deny()
    route name為passport.authorizations.deny

        3.4.6 AuthorizationController跑authorize()







目的：再進入oauth/authorize route之前，要先用middleware判斷帳號是否登入，遇到尚未登入時，卻沒有轉址到想要去的第三方登入頁

出發點：middleware (OauthAuthenticate.php)

1.
檔案路徑：app/Http/Middleware/OauthAuthenticate.php
function：redirectTo()
用途：設定當尚未登入時，要轉址到的route
     -> 在Authenticate.php中的unauthenticated()使用到

2.
檔案路徑：vendor/laravel/framework/src/Illuminate/Auth/Middleware/Authenticate.php
function：unauthenticated()
用途：處理尚未登入的使用者，該前往哪裡
做了什麼事：丟了一個exception，AuthenticationException()

3.
檔案路徑：vendor/laravel/framework/src/Illuminate/Auth/AuthenticationException.php
