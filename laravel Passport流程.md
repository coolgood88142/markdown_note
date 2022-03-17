1. 在Kai Blog按下FB登入
   
   passport.addEventListener("click", function() {})
   
   laravel_client\resources\views\welcome.blade.php 
   
   1.1 window.location.href = http://127.0.0.1:8080/authorize 網頁導頁到http://127.0.0.1:8080/authorize

2. localhost:8080/authorize 跑passport的oauth\authorize
   
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

3. localhost:8000/oauth/authorize 跑第三方登入
   
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
