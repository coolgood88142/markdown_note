# # L5 Swagger

## 大綱

- 什麼是L5 Swagger?
- 範例
- 參考資料

## 什麼是L5 Swagger

提供開發者可以快速完成API文件的撰寫需求，主要是能有效提升開發者之間對於接口的使用。

## 安裝

以下示範用laravel 6安裝

**安裝套件**

```
composer require "darkaonline/l5-swagger:6.*"
```

如果有需要用舊的寫法**SWAGGER**，需要再安裝套件

```
composer require 'zircote/swagger-php:2.*'
```

**config**

在config/app.php的providers，新增這行

```
L5Swagger\L5SwaggerServiceProvider::class
```

設定env

新增這行

```
 L5_SWAGGER_GENERATE_ALWAYS=true
```

新增資訊

在Controller.php，新增這行

```
/**
 * @SWG\Swagger(
 *     @SWG\Info(
 *         version="1.0.0",
 *         title="Laravel Passport Authorization Code API",
 *         description="Authorization Code API description",
 *         @SWG\Contact(
 *             email="coolgood88142@gmail.com"
 *         ),
 *     )
 * )
 */
```

產生文件

```
php artisan l5-swagger:generate
```

啟動server，打api 文件route

```
php artisan serve
http://127.0.0.1:8080/api/documentation
```

![](C:\xampp\htdocs\markdown_note\assets\images\swagger1.png)

### 範例

以下示範，用laravel passport做第三方登入

在AuthorizationController補充這行

```
    /**
     * @SWG\Get(
     *     path="/authorizationCode",
     *     summary="取得authorizationCode",
     *     tags={"AuthorizationCode"},
     *     produces={"application/json"},
     *     @SWG\Parameter(
     *          name="client_id",
     *          in="query",
     *          description="client端ID",
     *          required=true,
     *          type="string",
     *     ),
     *     @SWG\Parameter(
     *          name="redirect_uri",
     *          in="query",
     *          description="導頁的Uri",
     *          required=true,
     *          type="string",
     *     ),
     *     @SWG\Parameter(
     *          name="response_type",
     *          in="query",
     *          description="授權類型",
     *          required=true,
     *          type="string",
     *     ),
     *     @SWG\Parameter(
     *          name="scope",
     *          in="query",
     *          description="存取資料範圍",
     *          required=false,
     *          type="string",
     *     ),
     *     @SWG\Parameter(
     *          name="state",
     *          in="query",
     *          description="授權編碼",
     *          required=true,
     *          type="string",
     *     ),
     *     @SWG\Response(
     *          response="200",
     *          description="Successful creation",
     *     )
     * )
     */
```

在UserController補充這行

```
/**
     * @SWG\Get(
     *     path="/api/user",
     *     summary="取得使用者資訊",
     *     tags={"UserInfo"},
     *     produces={"application/json"},
     *      @SWG\Response(
     *          response=200,
     *          description="Successful operation",
     *      ),
     *     @SWG\Response(
     *          response="401",
     *          description="Unauthenticated",
     *     ),
     *     @SWG\Response(
     *          response="403",
     *          description="Forbidden",
     *     ),
     *     @SWG\Response(
     *          response="400",
     *          description="Bad Request",
     *     ),
     *     @SWG\Response(
     *          response="404",
     *          description="not found",
     *     ),
     * )
     */
```

### 參考資料

- [GitHub - DarkaOnLine/L5-Swagger: OpenApi or Swagger integration to Laravel](https://github.com/DarkaOnLine/L5-Swagger)
