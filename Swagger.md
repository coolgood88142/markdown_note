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

**設定env**

新增這幾行

```
 L5_SWAGGER_GENERATE_ALWAYS=true  //啟動swagger
 SWAGGER_VERSION=3.0    //swagger版本為3.0
```

新增資訊

新增app/Docs/Info.php，存放swagger資訊

```
<?php
/**
**
* @OA\OpenApi(
*   @OA\Info(
*      title="Swagger-Demo",
*      version="1.0.0",
*   ),
* )
*
*
* @OA\Tag(name="AuthorizationCode", description="取得authorizationCode")
* @OA\Tag(name="AccessToken", description="取得Access Token")
* @OA\Tag(name="UserInfo", description="取得UserInfo")
*/
```

新增app/Docs/Server.php，存放swagger資訊

```php
<?php
/**
* @OA\Server(
*       url = "http://127.0.0.1:8000",
*       description="local"
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

在AuthorizationController的class上面補充這行

```
    /**
     * @OA\Get(
     *     path="/authorizationCode",
     *     tags={"AuthorizationCode"},
     *     @OA\Parameter(
     *          name="client_id",
     *          in="query",
     *          description="client端ID",
     *          required=true,
     *          @OA\Schema(
     *              type="string"
     *          )
     *     ),
     *     @OA\Parameter(
     *          name="redirect_uri",
     *          in="query",
     *          description="導頁的Uri",
     *          required=true,
     *          @OA\Schema(
     *              type="string"
     *          )
     *     ),
     *     @OA\Parameter(
     *          name="response_type",
     *          in="query",
     *          description="授權類型",
     *          required=true,
     *          @OA\Schema(
     *              type="string"
     *          )
     *     ),
     *     @OA\Parameter(
     *          name="scope",
     *          in="query",
     *          description="存取資料範圍",
     *          @OA\Schema(
     *              type="string"
     *          )
     *     ),
     *     @OA\Parameter(
     *          name="state",
     *          in="query",
     *          description="授權編碼",
     *          required=true,
     *          @OA\Schema(
     *              type="string"
     *          )
     *     ),
     *     @OA\Response(
     *          response="200",
     *          description="Successful creation"
     *     ),
     *     @OA\Response(
     *        response=401,
     *        description="Unauthenticated"
     *     ),
     *     @OA\Response(
     *        response=400,
     *        description="Bad Request"
     *     ),
     *     @OA\Response(
     *        response=404,
     *        description="not found"
     *     ),
     *     @OA\Response(
     *        response=403,
     *        description="Forbidden"
     *     )
     * )
     */
```

在Controller的class上面補充這行

```
/**
 *  @OA\Post(
 *     path="/oauth/token",
 *     tags={"AccessToken"},
 *     @OA\RequestBody(
 *        @OA\MediaType(
 *           mediaType="multipart/form-data",
 *           @OA\Schema(
 *              @OA\Property(
 *                  property="grant_type",
 *                  type="string"
 *              ),
 *              @OA\Property(
 *                  property="redirect_uri",
 *                  type="string"
 *              ),
 *              @OA\Property(
 *                  property="code",
 *                  type="string"
 *              ),
 *              @OA\Property(
 *                  property="client_id",
 *                  type="Integer"
 *              ),
 *              @OA\Property(
 *                  property="client_secret",
 *                  type="string"
 *              ),
 *              example={
 *                  "grant_type": "authorization_code",
 *                  "redirect_uri": "http://127.0.0.1:8080/getAuthorizationCode",
 *                  "code": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
 *                  "client_id": 18,
 *                  "client_secret": "har2ePG2kSTW2BPNaqVWdiyhg5U3SarVxNpLxZgD"
 *              }
 *           )
 *        )
 *     ),
 *     @OA\Response(
 *         response=200,
 *         description="Successful operation",
 *     )
 *  )
 */
```

在UserController補充這行

```
/**
     **
     * @OA\Get(
     *     path="/api/user",
     *     tags={"UserInfo"},
     *     @OA\Response(
     *          response=200,
     *          description="Successful operation",
     *     ),
     *     @OA\Response(
     *          response="401",
     *          description="Unauthenticated",
     *     ),
     *     @OA\Response(
     *          response="403",
     *          description="Forbidden",
     *     ),
     *     @OA\Response(
     *          response="400",
     *          description="Bad Request",
     *     ),
     *     @OA\Response(
     *          response="404",
     *          description="not found",
     *     ),
     *     security={
     *          {"passport":{}}
     *     },
     * )
     *
     */
```



### 參考資料

- [GitHub - DarkaOnLine/L5-Swagger: OpenApi or Swagger integration to Laravel](https://github.com/DarkaOnLine/L5-Swagger)
